---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Explore ASP.NET Core Blazor , uma maneira de criar interface do usuário da Web interativa do lado do cliente com o .net em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: 560fead9868bab9888c0d6a69cf09f135bbf39cc
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279740"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="b03eb-103">Introdução ao ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b03eb-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="b03eb-104">*Bem-vindo ao Blazor !*</span><span class="sxs-lookup"><span data-stu-id="b03eb-104">*Welcome to Blazor!*</span></span>

<span data-ttu-id="b03eb-105">Blazor é uma estrutura para a criação de interface do usuário da Web interativa do lado do cliente com o [.net](/dotnet/standard/tour):</span><span class="sxs-lookup"><span data-stu-id="b03eb-105">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="b03eb-106">Crie interfaces de uso avançadas e interativas usando [C#](/dotnet/csharp/) em vez de [JavaScript](https://www.javascript.com).</span><span class="sxs-lookup"><span data-stu-id="b03eb-106">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="b03eb-107">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.</span><span class="sxs-lookup"><span data-stu-id="b03eb-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="b03eb-108">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="b03eb-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="b03eb-109">Integre-se com plataformas de hospedagem modernas, como o [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="b03eb-109">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="b03eb-110">Usar o .NET para desenvolvimento web do lado do cliente oferece as seguintes vantagens:</span><span class="sxs-lookup"><span data-stu-id="b03eb-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="b03eb-111">escreva o código em C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b03eb-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="b03eb-112">Aproveite o ecossistema .NET existente das [bibliotecas .net](/dotnet/standard/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="b03eb-112">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="b03eb-113">Compartilhe a lógica de aplicativo entre o servidor e o cliente.</span><span class="sxs-lookup"><span data-stu-id="b03eb-113">Share app logic across server and client.</span></span>
* <span data-ttu-id="b03eb-114">Beneficie-se com o desempenho, confiabilidade e segurança do .NET.</span><span class="sxs-lookup"><span data-stu-id="b03eb-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="b03eb-115">Mantenha-se produtivo com o [Visual Studio](https://visualstudio.microsoft.com) no Windows, no Linux e no MacOS.</span><span class="sxs-lookup"><span data-stu-id="b03eb-115">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="b03eb-116">Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.</span><span class="sxs-lookup"><span data-stu-id="b03eb-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="b03eb-117">Componentes</span><span class="sxs-lookup"><span data-stu-id="b03eb-117">Components</span></span>

<span data-ttu-id="b03eb-118">Blazor os aplicativos são baseados em *componentes*.</span><span class="sxs-lookup"><span data-stu-id="b03eb-118">Blazor apps are based on *components*.</span></span> <span data-ttu-id="b03eb-119">Um componente no Blazor é um elemento da interface do usuário, como um formulário de página, caixa de diálogo ou entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="b03eb-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="b03eb-120">Os componentes são classes .NET C# embutidas em [assemblies .net](/dotnet/standard/assembly/) que:</span><span class="sxs-lookup"><span data-stu-id="b03eb-120">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="b03eb-121">Definem a lógica de renderização da interface de usuário flexível.</span><span class="sxs-lookup"><span data-stu-id="b03eb-121">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="b03eb-122">Tratam eventos do usuário.</span><span class="sxs-lookup"><span data-stu-id="b03eb-122">Handle user events.</span></span>
* <span data-ttu-id="b03eb-123">Podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="b03eb-123">Can be nested and reused.</span></span>
* <span data-ttu-id="b03eb-124">Pode ser compartilhado e distribuído como [ Razor bibliotecas de classes](xref:razor-pages/ui-class) ou [pacotes NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="b03eb-124">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="b03eb-125">A classe de componente geralmente é escrita na forma de uma [Razor](xref:mvc/views/razor) página de marcação com uma `.razor` extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="b03eb-125">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="b03eb-126">Os componentes no Blazor são formalmente chamados de *Razor componentes*.</span><span class="sxs-lookup"><span data-stu-id="b03eb-126">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="b03eb-127">Razor é uma sintaxe para combinar marcação HTML com código C# projetado para a produtividade do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="b03eb-127">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="b03eb-128">Razor permite que você alterne entre marcação HTML e C# no mesmo arquivo com suporte à programação [IntelliSense](/visualstudio/ide/using-intellisense) no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b03eb-128">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="b03eb-129">Razor As páginas e MVC também usam o Razor .</span><span class="sxs-lookup"><span data-stu-id="b03eb-129">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="b03eb-130">Ao contrário de Razor páginas e MVC, que são criadas em um modelo de solicitação/resposta, os componentes são usados especificamente para a lógica e a composição da interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b03eb-130">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="b03eb-131">Blazor usa marcas HTML naturais para composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b03eb-131">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="b03eb-132">A marcação a seguir Razor demonstra um componente ( `Dialog.razor` ) que exibe uma caixa de diálogo e processa um evento quando o usuário seleciona um botão:</span><span class="sxs-lookup"><span data-stu-id="b03eb-132">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="b03eb-133">No exemplo anterior, `OnYes` é um método C# disparado pelo evento do botão `onclick` .</span><span class="sxs-lookup"><span data-stu-id="b03eb-133">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="b03eb-134">O texto () e o título () da caixa de diálogo `ChildContent` `Title` são fornecidos pelo seguinte componente que usa esse componente em sua interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b03eb-134">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="b03eb-135">O `Dialog` componente é aninhado dentro de outro componente usando uma marca HTML.</span><span class="sxs-lookup"><span data-stu-id="b03eb-135">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="b03eb-136">No exemplo a seguir, o `Index` componente ( `Pages/Index.razor` ) usa o `Dialog` componente anterior.</span><span class="sxs-lookup"><span data-stu-id="b03eb-136">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="b03eb-137">O atributo da marca `Title` passa um valor para o título para a `Dialog` Propriedade do componente `Title` .</span><span class="sxs-lookup"><span data-stu-id="b03eb-137">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="b03eb-138">O `Dialog` texto do componente ( `ChildContent` ) é definido pelo conteúdo do `<Dialog>` elemento.</span><span class="sxs-lookup"><span data-stu-id="b03eb-138">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="b03eb-139">Quando o `Dialog` componente é adicionado ao `Index` componente, o [IntelliSense no Visual Studio](/visualstudio/ide/using-intellisense) agiliza o desenvolvimento com a conclusão da sintaxe e do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b03eb-139">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="b03eb-140">A caixa de diálogo é renderizada quando o `Index` componente é acessado em um navegador.</span><span class="sxs-lookup"><span data-stu-id="b03eb-140">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="b03eb-141">Quando o botão é selecionado pelo usuário, o console de ferramentas de desenvolvedor do navegador mostra a mensagem escrita pelo `OnYes` método:</span><span class="sxs-lookup"><span data-stu-id="b03eb-141">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Componente de caixa de diálogo renderizado no navegador aninhado dentro do componente de índice.](index/_static/dialog.png)

<span data-ttu-id="b03eb-145">Os componentes são renderizados em uma representação na memória do Modelo de Objeto do Documento do navegador [(dom)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) chamado de *árvore de renderização*, que é usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="b03eb-145">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="b03eb-146">Blazor WebAssembly o é uma [estrutura de Spa (aplicativo de página única)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) para a criação de aplicativos Web do lado do cliente interativos com o .net.</span><span class="sxs-lookup"><span data-stu-id="b03eb-146">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="b03eb-147">Blazor WebAssembly usa padrões abertos da Web sem plugins ou recompilando código em outras linguagens.</span><span class="sxs-lookup"><span data-stu-id="b03eb-147">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="b03eb-148">Blazor WebAssembly funciona em todos os navegadores da Web modernos, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="b03eb-148">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="b03eb-149">A execução de código .NET dentro de navegadores da Web é possibilitada pelo [Webassembly](https://webassembly.org) (abreviado `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="b03eb-149">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="b03eb-150">O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.</span><span class="sxs-lookup"><span data-stu-id="b03eb-150">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="b03eb-151">O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.</span><span class="sxs-lookup"><span data-stu-id="b03eb-151">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="b03eb-152">O código Webassembly pode acessar a funcionalidade completa do navegador via JavaScript, chamada de *interoperabilidade de JavaScript*, geralmente reduzida para interoperabilidade de *JavaScript* ou de *js*.</span><span class="sxs-lookup"><span data-stu-id="b03eb-152">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="b03eb-153">O código .NET executado por meio da WebAssembly no navegador é executado na área restrita do JavaScript do navegador com as proteções que a área restrita oferece contra ações mal intencionadas no computador cliente.</span><span class="sxs-lookup"><span data-stu-id="b03eb-153">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: no-Loc (Webassembly do mais alto)::: executa o código .NET no navegador com Webassembly.](index/_static/blazor-webassembly.png)

<span data-ttu-id="b03eb-155">Quando um Blazor WebAssembly aplicativo é compilado e executado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="b03eb-155">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="b03eb-156">Arquivos de código C# e Razor arquivos são compilados em assemblies .net.</span><span class="sxs-lookup"><span data-stu-id="b03eb-156">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="b03eb-157">Os assemblies e o [tempo de execução do .net](/dotnet/framework/get-started/overview) são baixados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="b03eb-157">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="b03eb-158">Blazor WebAssembly Inicializa o tempo de execução do .NET e configura o tempo de execução para carregar os assemblies para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b03eb-158">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="b03eb-159">O Blazor WebAssembly tempo de execução usa a interoperabilidade JavaScript para lidar com a manipulação de Dom e chamadas de API de navegador</span><span class="sxs-lookup"><span data-stu-id="b03eb-159">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="b03eb-160">O tamanho do aplicativo publicado, seu *tamanho de carga*, é um fator de desempenho crítico para a usabilidade de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b03eb-160">The size of the published app, its *payload size*, is a critical performance factor for an app's usability.</span></span> <span data-ttu-id="b03eb-161">Um aplicativo grande leva um tempo relativamente longo para baixar para um navegador, o que afeta a experiência do usuário.</span><span class="sxs-lookup"><span data-stu-id="b03eb-161">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="b03eb-162">Blazor WebAssembly otimiza o tamanho da carga para reduzir os tempos de download:</span><span class="sxs-lookup"><span data-stu-id="b03eb-162">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="b03eb-163">O código não utilizado é removido do aplicativo quando é publicado pela [corte de Il (linguagem intermediária)](xref:blazor/host-and-deploy/configure-trimmer).</span><span class="sxs-lookup"><span data-stu-id="b03eb-163">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="b03eb-164">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="b03eb-164">HTTP responses are compressed.</span></span>
* <span data-ttu-id="b03eb-165">O runtime do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="b03eb-165">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="b03eb-166">O código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="b03eb-166">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="b03eb-167">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="b03eb-167">HTTP responses are compressed.</span></span>
* <span data-ttu-id="b03eb-168">O runtime do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="b03eb-168">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="b03eb-169">Blazor dissocia a lógica de renderização do componente de como as atualizações da interface do usuário são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="b03eb-169">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="b03eb-170">*Blazor Server* fornece suporte para hospedar Razor componentes no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b03eb-170">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="b03eb-171">As atualizações da interface do usuário são manipuladas em uma [SignalR](xref:signalr/introduction) conexão.</span><span class="sxs-lookup"><span data-stu-id="b03eb-171">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="b03eb-172">O tempo de execução permanece no servidor e manipula:</span><span class="sxs-lookup"><span data-stu-id="b03eb-172">The runtime stays on the server and handles:</span></span>

* <span data-ttu-id="b03eb-173">Executando o código C# do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b03eb-173">Executing the app's C# code.</span></span>
* <span data-ttu-id="b03eb-174">Enviando eventos de interface do usuário do navegador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="b03eb-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="b03eb-175">Aplicando atualizações de interface do usuário ao componente renderizado que são enviados de volta pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="b03eb-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="b03eb-176">A conexão usada pelo Blazor Server para se comunicar com o navegador também é usada para lidar com as chamadas de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b03eb-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: no-Loc (servidor de maior número)::: executa o código .NET no servidor e interage com o Modelo de Objeto do Documento no cliente sobre uma::: no-Loc (Signalr)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="b03eb-178">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="b03eb-178">JavaScript interop</span></span>

<span data-ttu-id="b03eb-179">Para aplicativos que exigem bibliotecas JavaScript e acesso a APIs do navegador de terceiros, os componentes interoperam com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b03eb-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="b03eb-180">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="b03eb-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="b03eb-181">O código c# pode chamar o código [JavaScript](xref:blazor/call-javascript-from-dotnet)e o código JavaScript pode [chamar código C#](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="b03eb-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="b03eb-182">Compartilhamento de código e o .NET Standard</span><span class="sxs-lookup"><span data-stu-id="b03eb-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="b03eb-183">Blazor implementa o [.net Standard](/dotnet/standard/net-standard), que permite que os Blazor projetos referenciem bibliotecas que estão em conformidade com .net Standard especificações.</span><span class="sxs-lookup"><span data-stu-id="b03eb-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="b03eb-184">O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET.</span><span class="sxs-lookup"><span data-stu-id="b03eb-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="b03eb-185">.NET Standard bibliotecas de classe podem ser compartilhadas entre diferentes plataformas .NET, como Blazor , .NET Framework, .NET Core, Xamarin, mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="b03eb-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="b03eb-186">As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete e threading) geram a <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="b03eb-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b03eb-187">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b03eb-187">Additional resources</span></span>

* [<span data-ttu-id="b03eb-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b03eb-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="b03eb-189">Guia do C#</span><span class="sxs-lookup"><span data-stu-id="b03eb-189">C# Guide</span></span>](/dotnet/csharp/)
* [<span data-ttu-id="b03eb-190">Razor referência de sintaxe para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b03eb-190">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* [<span data-ttu-id="b03eb-191">HTML</span><span class="sxs-lookup"><span data-stu-id="b03eb-191">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="b03eb-192">[Incrível Blazor ](https://github.com/AdrienTorris/awesome-blazor) links da Comunidade</span><span class="sxs-lookup"><span data-stu-id="b03eb-192">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
