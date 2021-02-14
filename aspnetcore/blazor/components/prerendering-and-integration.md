---
title: PreRender e integre componentes de ASP.NET Core Razor
author: guardrex
description: Saiba mais sobre Razor cenários de integração de componentes para Blazor aplicativos, incluindo a renderização de Razor componentes no servidor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d120440c292d15b7741260ed31af92d60db2261c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280063"
---
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a><span data-ttu-id="b7235-103">PreRender e integre componentes de ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="b7235-103">Prerender and integrate ASP.NET Core Razor components</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b7235-104">Razor os componentes podem ser integrados em Razor páginas e aplicativos MVC em uma solução hospedada Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b7235-104">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="b7235-105">Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="b7235-105">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="b7235-106">Configuração</span><span class="sxs-lookup"><span data-stu-id="b7235-106">Configuration</span></span>

<span data-ttu-id="b7235-107">Para configurar o pré-processamento para um Blazor WebAssembly aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b7235-107">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="b7235-108">Hospede o Blazor WebAssembly aplicativo em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7235-108">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="b7235-109">Um Blazor WebAssembly aplicativo autônomo pode ser adicionado a uma solução de ASP.NET Core, ou você pode usar um Blazor WebAssembly aplicativo hospedado criado a partir do Blazor modelo de projeto hospedado.</span><span class="sxs-lookup"><span data-stu-id="b7235-109">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the Blazor Hosted project template.</span></span>

1. <span data-ttu-id="b7235-110">Remova o arquivo estático padrão `wwwroot/index.html` do Blazor WebAssembly projeto do cliente.</span><span class="sxs-lookup"><span data-stu-id="b7235-110">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="b7235-111">Exclua a seguinte linha no `Program.Main` projeto do cliente:</span><span class="sxs-lookup"><span data-stu-id="b7235-111">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="b7235-112">Adicione um `Pages/_Host.cshtml` arquivo ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="b7235-112">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="b7235-113">Você pode obter um `_Host.cshtml` arquivo de um aplicativo criado a partir do Blazor Server modelo com o `dotnet new blazorserver -o BlazorServer` comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="b7235-113">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="b7235-114">Depois de colocar o `Pages/_Host.cshtml` arquivo no aplicativo de servidor da solução hospedada Blazor WebAssembly , faça as seguintes alterações no arquivo:</span><span class="sxs-lookup"><span data-stu-id="b7235-114">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="b7235-115">Defina o namespace para a pasta do aplicativo do servidor `Pages` (por exemplo, `@namespace BlazorHosted.Server.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="b7235-115">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="b7235-116">Defina uma [`@using`](xref:mvc/views/razor#using) diretiva para o projeto cliente (por exemplo, `@using BlazorHosted.Client` ).</span><span class="sxs-lookup"><span data-stu-id="b7235-116">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="b7235-117">Atualize os links de folha de estilos para apontar para a folha de estilos do aplicativo Webassembly.</span><span class="sxs-lookup"><span data-stu-id="b7235-117">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="b7235-118">No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b7235-118">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="b7235-119">Atualize o `render-mode` do [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para PreRender o `App` componente raiz:</span><span class="sxs-lookup"><span data-stu-id="b7235-119">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="b7235-120">Atualize a Blazor origem do script para usar o script do lado do cliente Blazor WebAssembly :</span><span class="sxs-lookup"><span data-stu-id="b7235-120">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="b7235-121">No `Startup.Configure` ( `Startup.cs` ) do projeto do servidor:</span><span class="sxs-lookup"><span data-stu-id="b7235-121">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="b7235-122">Chame `UseDeveloperExceptionPage` no app Builder no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b7235-122">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="b7235-123">Chame `UseBlazorFrameworkFiles` no app Builder.</span><span class="sxs-lookup"><span data-stu-id="b7235-123">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="b7235-124">Altere o fallback da `index.html` página ( `endpoints.MapFallbackToFile("index.html");` ) para a `_Host.cshtml` página.</span><span class="sxs-lookup"><span data-stu-id="b7235-124">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="b7235-125">Renderizar componentes em uma página ou exibição com o auxiliar de marca do componente</span><span class="sxs-lookup"><span data-stu-id="b7235-125">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="b7235-126">O auxiliar de marca de componente dá suporte a dois modos de renderização para renderizar um componente de um Blazor WebAssembly aplicativo em uma página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="b7235-126">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="b7235-127">`WebAssembly`: Renderiza um marcador para um Blazor WebAssembly aplicativo para usar para incluir um componente interativo quando carregado no navegador.</span><span class="sxs-lookup"><span data-stu-id="b7235-127">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="b7235-128">O componente não é renderizado.</span><span class="sxs-lookup"><span data-stu-id="b7235-128">The component isn't prerendered.</span></span> <span data-ttu-id="b7235-129">Essa opção torna mais fácil renderizar diferentes Blazor WebAssembly componentes em páginas diferentes.</span><span class="sxs-lookup"><span data-stu-id="b7235-129">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="b7235-130">`WebAssemblyPrerendered`: Coloca o componente em HTML estático e inclui um marcador para um Blazor WebAssembly aplicativo para uso posterior para tornar o componente interativo quando carregado no navegador.</span><span class="sxs-lookup"><span data-stu-id="b7235-130">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="b7235-131">No exemplo de Razor páginas a seguir, o `Counter` componente é renderizado em uma página.</span><span class="sxs-lookup"><span data-stu-id="b7235-131">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="b7235-132">Para tornar o componente interativo, o Blazor WebAssembly script é incluído na [seção renderizar](xref:mvc/views/layout#sections)da página.</span><span class="sxs-lookup"><span data-stu-id="b7235-132">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="b7235-133">Para evitar o uso do namespace completo para o `Counter` componente com o auxiliar de marca de componente ( `{APP ASSEMBLY}.Pages.Counter` ), adicione uma [`@using`](xref:mvc/views/razor#using) diretiva para o namespace do aplicativo cliente `Pages` .</span><span class="sxs-lookup"><span data-stu-id="b7235-133">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="b7235-134">No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b7235-134">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="b7235-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="b7235-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="b7235-136">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="b7235-136">Is prerendered into the page.</span></span>
* <span data-ttu-id="b7235-137">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="b7235-137">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="b7235-138">Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="b7235-138">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="b7235-139">O exemplo anterior requer que o layout do aplicativo do servidor ( `_Layout.cshtml` ) inclua uma [seção render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) para o script dentro da `</body>` marca de fechamento:</span><span class="sxs-lookup"><span data-stu-id="b7235-139">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="b7235-140">O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um Razor aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="b7235-140">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="b7235-141">Se o aplicativo também deve estilizar componentes com os estilos no Blazor WebAssembly aplicativo, inclua os estilos do aplicativo no `_Layout.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="b7235-141">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="b7235-142">No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b7235-142">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="b7235-143">Renderizar componentes em uma página ou exibição com um seletor de CSS</span><span class="sxs-lookup"><span data-stu-id="b7235-143">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="b7235-144">Adicione componentes raiz ao projeto do *cliente* no `Program.Main` ( `Program.cs` ).</span><span class="sxs-lookup"><span data-stu-id="b7235-144">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="b7235-145">No exemplo a seguir, o `Counter` componente é declarado como um componente raiz com um seletor CSS que seleciona o elemento com o `id` que corresponde a `my-counter` .</span><span class="sxs-lookup"><span data-stu-id="b7235-145">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="b7235-146">No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b7235-146">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="b7235-147">No exemplo de Razor páginas a seguir, o `Counter` componente é renderizado em uma página.</span><span class="sxs-lookup"><span data-stu-id="b7235-147">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="b7235-148">Para tornar o componente interativo, o Blazor WebAssembly script é incluído na [seção renderizar](xref:mvc/views/layout#sections)da página:</span><span class="sxs-lookup"><span data-stu-id="b7235-148">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="b7235-149">O exemplo anterior requer que o layout do aplicativo do servidor ( `_Layout.cshtml` ) inclua uma [seção render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) para o script dentro da `</body>` marca de fechamento:</span><span class="sxs-lookup"><span data-stu-id="b7235-149">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="b7235-150">O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um Razor aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="b7235-150">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="b7235-151">Se o aplicativo também deve estilizar componentes com os estilos no Blazor WebAssembly aplicativo, inclua os estilos do aplicativo no `_Layout.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="b7235-151">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="b7235-152">No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b7235-152">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b7235-153">A integração de Razor componentes em Razor páginas e aplicativos MVC em uma solução hospedada Blazor WebAssembly tem suporte no ASP.NET Core no .NET 5 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="b7235-153">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="b7235-154">Selecione uma versão do .NET 5 ou posterior deste artigo.</span><span class="sxs-lookup"><span data-stu-id="b7235-154">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="b7235-155">Razor os componentes podem ser integrados em Razor páginas e aplicativos MVC em um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7235-155">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="b7235-156">Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="b7235-156">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="b7235-157">Depois de [Configurar o aplicativo](#configuration), use as diretrizes nas seções a seguir, dependendo dos requisitos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b7235-157">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="b7235-158">Componentes roteáveis: para componentes que são roteáveis diretamente das solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="b7235-158">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="b7235-159">Siga estas diretrizes para que os visitantes possam fazer uma solicitação HTTP em seu navegador para um componente com uma [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="b7235-159">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="b7235-160">Usar componentes roteáveis em um Razor aplicativo de páginas</span><span class="sxs-lookup"><span data-stu-id="b7235-160">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="b7235-161">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="b7235-161">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="b7235-162">[Renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view): para componentes que não são roteáveis diretamente das solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="b7235-162">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="b7235-163">Siga estas diretrizes quando o aplicativo inserir componentes em páginas e exibições existentes com o [auxiliar de marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b7235-163">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="b7235-164">Configuração</span><span class="sxs-lookup"><span data-stu-id="b7235-164">Configuration</span></span>

<span data-ttu-id="b7235-165">Uma Razor página existente ou um aplicativo MVC pode integrar Razor componentes em páginas e exibições:</span><span class="sxs-lookup"><span data-stu-id="b7235-165">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="b7235-166">No arquivo de layout do aplicativo ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="b7235-166">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="b7235-167">Adicione a seguinte `<base>` marcação ao `<head>` elemento:</span><span class="sxs-lookup"><span data-stu-id="b7235-167">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="b7235-168">O `href` valor (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="b7235-168">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="b7235-169">Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do <xref:blazor/host-and-deploy/index#app-base-path> artigo.</span><span class="sxs-lookup"><span data-stu-id="b7235-169">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="b7235-170">O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um Razor aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="b7235-170">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="b7235-171">Adicione uma `<script>` marca para o `blazor.server.js` script imediatamente antes da `Scripts` seção render:</span><span class="sxs-lookup"><span data-stu-id="b7235-171">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="b7235-172">A estrutura adiciona o `blazor.server.js` script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7235-172">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="b7235-173">Não é necessário adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7235-173">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="b7235-174">Adicione um `_Imports.razor` arquivo à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace` , para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="b7235-174">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="b7235-175">No `Startup.ConfigureServices` , registre o Blazor Server serviço:</span><span class="sxs-lookup"><span data-stu-id="b7235-175">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="b7235-176">No `Startup.Configure` , adicione o Blazor ponto de extremidade do hub a `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="b7235-176">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="b7235-177">Integre componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="b7235-177">Integrate components into any page or view.</span></span> <span data-ttu-id="b7235-178">Para obter mais informações, consulte a seção [renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="b7235-178">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="b7235-179">Usar componentes roteáveis em um Razor aplicativo de páginas</span><span class="sxs-lookup"><span data-stu-id="b7235-179">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="b7235-180">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="b7235-180">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="b7235-181">Para dar suporte a componentes roteáveis Razor em Razor páginas aplicativos:</span><span class="sxs-lookup"><span data-stu-id="b7235-181">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="b7235-182">Siga as orientações na seção [configuração](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="b7235-182">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="b7235-183">Adicione um `App.razor` arquivo à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="b7235-183">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="b7235-184">Adicione um `_Host.cshtml` arquivo à `Pages` pasta com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="b7235-184">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="b7235-185">Os componentes usam o `_Layout.cshtml` arquivo compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="b7235-185">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="b7235-186"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o `App` componente:</span><span class="sxs-lookup"><span data-stu-id="b7235-186"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="b7235-187">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="b7235-187">Is prerendered into the page.</span></span>
   * <span data-ttu-id="b7235-188">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="b7235-188">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="b7235-189">Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="b7235-189">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="b7235-190">Adicione uma rota de baixa prioridade à `_Host.cshtml` página para a configuração de ponto de extremidade no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b7235-190">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="b7235-191">Adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7235-191">Add routable components to the app.</span></span> <span data-ttu-id="b7235-192">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b7235-192">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="b7235-193">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="b7235-193">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="b7235-194">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="b7235-194">Use routable components in an MVC app</span></span>

<span data-ttu-id="b7235-195">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="b7235-195">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="b7235-196">Para dar suporte a componentes roteáveis Razor em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="b7235-196">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="b7235-197">Siga as orientações na seção [configuração](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="b7235-197">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="b7235-198">Adicione um `App.razor` arquivo à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="b7235-198">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="b7235-199">Adicione um `_Host.cshtml` arquivo à `Views/Home` pasta com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="b7235-199">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="b7235-200">Os componentes usam o `_Layout.cshtml` arquivo compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="b7235-200">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="b7235-201"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o `App` componente:</span><span class="sxs-lookup"><span data-stu-id="b7235-201"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="b7235-202">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="b7235-202">Is prerendered into the page.</span></span>
   * <span data-ttu-id="b7235-203">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="b7235-203">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="b7235-204">Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="b7235-204">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="b7235-205">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="b7235-205">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="b7235-206">Adicione uma rota de baixa prioridade para a ação do controlador que retorna a `_Host.cshtml` exibição para a configuração do ponto de extremidade no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b7235-206">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="b7235-207">Crie uma `Pages` pasta e adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7235-207">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="b7235-208">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b7235-208">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="b7235-209">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="b7235-209">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="b7235-210">Renderizar componentes de uma página ou exibição</span><span class="sxs-lookup"><span data-stu-id="b7235-210">Render components from a page or view</span></span>

<span data-ttu-id="b7235-211">*Esta seção pertence à adição de componentes a páginas ou exibições, em que os componentes não são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="b7235-211">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="b7235-212">Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b7235-212">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="b7235-213">Renderizar componentes interativos com estado</span><span class="sxs-lookup"><span data-stu-id="b7235-213">Render stateful interactive components</span></span>

<span data-ttu-id="b7235-214">Componentes interativos com estado podem ser adicionados a uma Razor página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="b7235-214">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="b7235-215">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="b7235-215">When the page or view renders:</span></span>

* <span data-ttu-id="b7235-216">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="b7235-216">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="b7235-217">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="b7235-217">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="b7235-218">O novo estado do componente é criado quando a SignalR conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="b7235-218">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="b7235-219">A página a seguir Razor renderiza um `Counter` componente:</span><span class="sxs-lookup"><span data-stu-id="b7235-219">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="b7235-220">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="b7235-220">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="b7235-221">Renderizar componentes não interativos</span><span class="sxs-lookup"><span data-stu-id="b7235-221">Render noninteractive components</span></span>

<span data-ttu-id="b7235-222">Na página a seguir Razor , o `Counter` componente é processado estaticamente com um valor inicial que é especificado usando um formulário.</span><span class="sxs-lookup"><span data-stu-id="b7235-222">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="b7235-223">Como o componente é processado estaticamente, o componente não é interativo:</span><span class="sxs-lookup"><span data-stu-id="b7235-223">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="b7235-224">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="b7235-224">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="b7235-225">Namespaces de componente</span><span class="sxs-lookup"><span data-stu-id="b7235-225">Component namespaces</span></span>

<span data-ttu-id="b7235-226">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao `_ViewImports.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="b7235-226">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="b7235-227">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b7235-227">In the following example:</span></span>

* <span data-ttu-id="b7235-228">Altere `MyAppNamespace` para o namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7235-228">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="b7235-229">Se uma pasta chamada `Components` não for usada para manter os componentes, altere `Components` para a pasta onde residem os componentes.</span><span class="sxs-lookup"><span data-stu-id="b7235-229">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="b7235-230">O `_ViewImports.cshtml` arquivo está localizado na `Pages` pasta de um Razor aplicativo de páginas ou na `Views` pasta de um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="b7235-230">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="b7235-231">Para obter mais informações, consulte <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="b7235-231">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
