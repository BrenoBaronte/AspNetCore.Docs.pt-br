---
---
<span data-ttu-id="605ea-101">Embora um aplicativo de servidor mais incrivelmente seja o pré-processamento, determinadas ações, como a chamada para JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="605ea-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="605ea-102">Os componentes podem precisar ser renderizados de forma diferente quando renderizados.</span><span class="sxs-lookup"><span data-stu-id="605ea-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="605ea-103">Para atrasar as chamadas de interoperabilidade do JavaScript até que a conexão com o navegador seja estabelecida, você pode usar o [evento de ciclo de vida do componente OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="605ea-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="605ea-104">Esse evento é chamado somente depois que o aplicativo é totalmente renderizado e a conexão do cliente é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="605ea-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="605ea-105">Para o código de exemplo anterior, forneça uma `setElementText` função JavaScript dentro do `<head>` elemento de *wwwroot/index.html* (Webassembly de mais claro) ou *pages/_Host. cshtml* (servidor mais incrivelmente).</span><span class="sxs-lookup"><span data-stu-id="605ea-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="605ea-106">A função é chamada com <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="605ea-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="605ea-107">O exemplo anterior modifica o Modelo de Objeto do Documento (DOM) diretamente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="605ea-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="605ea-108">A modificação direta do DOM com o JavaScript não é recomendada na maioria dos cenários porque o JavaScript pode interferir no controle de alterações de mais grande.</span><span class="sxs-lookup"><span data-stu-id="605ea-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="605ea-109">O componente a seguir demonstra como usar a interoperabilidade do JavaScript como parte da lógica de inicialização de um componente de forma que seja compatível com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="605ea-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="605ea-110">O componente mostra que é possível disparar uma atualização de renderização de dentro do <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="605ea-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="605ea-111">O desenvolvedor deve evitar a criação de um loop infinito nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="605ea-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="605ea-112">Em que <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> é chamado, `ElementRef` é usado apenas em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e não em nenhum método de ciclo de vida anterior porque não há nenhum elemento JavaScript até que o componente seja renderizado.</span><span class="sxs-lookup"><span data-stu-id="605ea-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="605ea-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) é chamado para reprocessar o componente com o novo estado obtido da chamada de interoperabilidade JavaScript.</span><span class="sxs-lookup"><span data-stu-id="605ea-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="605ea-114">O código não cria um loop infinito porque `StateHasChanged` é chamado somente quando `infoFromJs` é `null` .</span><span class="sxs-lookup"><span data-stu-id="605ea-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="605ea-115">Para o código de exemplo anterior, forneça uma `setElementText` função JavaScript dentro do `<head>` elemento de *wwwroot/index.html* (Webassembly de mais claro) ou *pages/_Host. cshtml* (servidor mais incrivelmente).</span><span class="sxs-lookup"><span data-stu-id="605ea-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="605ea-116">A função é chamada com <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="605ea-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="605ea-117">O exemplo anterior modifica o Modelo de Objeto do Documento (DOM) diretamente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="605ea-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="605ea-118">A modificação direta do DOM com o JavaScript não é recomendada na maioria dos cenários porque o JavaScript pode interferir no controle de alterações de mais grande.</span><span class="sxs-lookup"><span data-stu-id="605ea-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
