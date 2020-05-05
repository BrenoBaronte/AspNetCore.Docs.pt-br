---
title: Link do navegador no ASP.NET Core
author: ncarandini
description: Explica como o link do navegador é um recurso do Visual Studio que vincula o ambiente de desenvolvimento com um ou mais navegadores da Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 619d19ba90298b2455d4a558fea138c86a751f07
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773651"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="09ae5-103">Link do navegador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="09ae5-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="09ae5-104">Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="09ae5-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="09ae5-105">O link do navegador é um recurso do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="09ae5-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="09ae5-106">Ele cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da Web.</span><span class="sxs-lookup"><span data-stu-id="09ae5-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="09ae5-107">Você pode usar o link do navegador para atualizar seu aplicativo Web em vários navegadores de uma vez, o que é útil para testes entre navegadores.</span><span class="sxs-lookup"><span data-stu-id="09ae5-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="09ae5-108">Instalação do link do navegador</span><span class="sxs-lookup"><span data-stu-id="09ae5-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="09ae5-109">Adicione o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="09ae5-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="09ae5-110">Para ASP.NET Core Razor páginas ou projetos MVC, habilite também a compilação Razor em tempo de execução de arquivos (*. cshtml*), conforme descrito em <xref:mvc/views/view-compilation>.</span><span class="sxs-lookup"><span data-stu-id="09ae5-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> Razor<span data-ttu-id="09ae5-111">as alterações de sintaxe são aplicadas somente quando a compilação em tempo de execução tiver sido habilitada.</span><span class="sxs-lookup"><span data-stu-id="09ae5-111"> syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="09ae5-112">Ao converter um projeto ASP.NET Core 2,0 para ASP.NET Core 2,1 e fazer a transição para o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), instale o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para a funcionalidade de link do navegador.</span><span class="sxs-lookup"><span data-stu-id="09ae5-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="09ae5-113">Os modelos de projeto ASP.NET Core 2,1 usam `Microsoft.AspNetCore.App` o metapacote por padrão.</span><span class="sxs-lookup"><span data-stu-id="09ae5-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="09ae5-114">Os modelos de projeto de **aplicativo web**ASP.NET Core 2,0, **vazio**e de **API Web** usam o [metapacote Microsoft. AspNetCore. All](xref:fundamentals/metapackage), que contém uma referência de pacote para [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="09ae5-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="09ae5-115">Portanto, o uso `Microsoft.AspNetCore.All` do metapacote não requer nenhuma ação adicional para disponibilizar o link do navegador para uso.</span><span class="sxs-lookup"><span data-stu-id="09ae5-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="09ae5-116">O modelo de projeto de **aplicativo Web** ASP.NET Core 1. x tem uma referência de pacote para o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="09ae5-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="09ae5-117">Outros tipos de projeto exigem que você adicione uma referência de `Microsoft.VisualStudio.Web.BrowserLink`pacote ao.</span><span class="sxs-lookup"><span data-stu-id="09ae5-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="09ae5-118">Configuração</span><span class="sxs-lookup"><span data-stu-id="09ae5-118">Configuration</span></span>

<span data-ttu-id="09ae5-119">Chame `UseBrowserLink` no método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="09ae5-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="09ae5-120">Normalmente `UseBrowserLink` , a chamada é colocada dentro `if` de um bloco que habilita apenas o link do navegador no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="09ae5-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="09ae5-121">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="09ae5-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="09ae5-122">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="09ae5-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="09ae5-123">Como usar o link do navegador</span><span class="sxs-lookup"><span data-stu-id="09ae5-123">How to use Browser Link</span></span>

<span data-ttu-id="09ae5-124">Quando você tem um projeto ASP.NET Core aberto, o Visual Studio mostra o controle da barra de ferramentas link do navegador ao lado do controle da barra de ferramentas de **destino de depuração** :</span><span class="sxs-lookup"><span data-stu-id="09ae5-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu suspenso de link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="09ae5-126">No controle da barra de ferramentas do link do navegador, você pode:</span><span class="sxs-lookup"><span data-stu-id="09ae5-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="09ae5-127">Atualize o aplicativo Web em vários navegadores de uma vez.</span><span class="sxs-lookup"><span data-stu-id="09ae5-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="09ae5-128">Abra o **painel de link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="09ae5-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="09ae5-129">Habilitar ou desabilitar o **link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="09ae5-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="09ae5-130">Observação: o link do navegador está desabilitado por padrão no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="09ae5-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="09ae5-131">Habilitar ou desabilitar [a sincronização automática de CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="09ae5-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="09ae5-132">Atualizar o aplicativo Web em vários navegadores de uma vez</span><span class="sxs-lookup"><span data-stu-id="09ae5-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="09ae5-133">Para escolher um único navegador da Web para iniciar ao iniciar o projeto, use o menu suspenso no controle da barra de ferramentas de **destino de depuração** :</span><span class="sxs-lookup"><span data-stu-id="09ae5-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 menu suspenso](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="09ae5-135">Para abrir vários navegadores de uma vez, escolha **procurar com...** na mesma lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="09ae5-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="09ae5-136">Mantenha pressionada a tecla <kbd>Ctrl</kbd> para selecionar os navegadores desejados e clique em **procurar**:</span><span class="sxs-lookup"><span data-stu-id="09ae5-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Abrir vários navegadores de uma vez](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="09ae5-138">A captura de tela a seguir mostra o Visual Studio com a exibição de índice aberta e dois navegadores abertos:</span><span class="sxs-lookup"><span data-stu-id="09ae5-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Exemplo de sincronização com dois navegadores](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="09ae5-140">Passe o mouse sobre o controle da barra de ferramentas link do navegador para ver os navegadores que estão conectados ao projeto:</span><span class="sxs-lookup"><span data-stu-id="09ae5-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Dica de foco](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="09ae5-142">Altere a exibição do índice e todos os navegadores conectados serão atualizados quando você clicar no botão atualizar do link do navegador:</span><span class="sxs-lookup"><span data-stu-id="09ae5-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![navegadores-sincronização-para-alterações](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="09ae5-144">O link do navegador também funciona com navegadores que você inicia de fora do Visual Studio e navega até a URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="09ae5-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="09ae5-145">O painel do link do navegador</span><span class="sxs-lookup"><span data-stu-id="09ae5-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="09ae5-146">Abra a janela do **Painel link do navegador** no menu suspenso link do navegador para gerenciar a conexão com os navegadores abertos:</span><span class="sxs-lookup"><span data-stu-id="09ae5-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Open-browserslink-Dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="09ae5-148">Se nenhum navegador estiver conectado, você poderá iniciar uma sessão de não depuração selecionando o link **Exibir no navegador** :</span><span class="sxs-lookup"><span data-stu-id="09ae5-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-Dashboard-no-Connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="09ae5-150">Caso contrário, os navegadores conectados são mostrados com o caminho para a página que cada navegador está mostrando:</span><span class="sxs-lookup"><span data-stu-id="09ae5-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-Dashboard-Two-Connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="09ae5-152">Você também pode clicar em um nome de navegador individual para atualizar somente esse navegador.</span><span class="sxs-lookup"><span data-stu-id="09ae5-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="09ae5-153">Habilitar ou desabilitar o link do navegador</span><span class="sxs-lookup"><span data-stu-id="09ae5-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="09ae5-154">Ao reabilitar o link do navegador depois de desabilitá-lo, você deve atualizar os navegadores para reconectá-los.</span><span class="sxs-lookup"><span data-stu-id="09ae5-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="09ae5-155">Habilitar ou desabilitar a sincronização automática de CSS</span><span class="sxs-lookup"><span data-stu-id="09ae5-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="09ae5-156">Quando a sincronização automática de CSS está habilitada, os navegadores conectados são atualizados automaticamente quando você faz qualquer alteração em arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="09ae5-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="09ae5-157">Como ele funciona</span><span class="sxs-lookup"><span data-stu-id="09ae5-157">How it works</span></span>

<span data-ttu-id="09ae5-158">O link do [SignalR](xref:signalr/introduction) navegador usa para criar um canal de comunicação entre o Visual Studio e o navegador.</span><span class="sxs-lookup"><span data-stu-id="09ae5-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="09ae5-159">Quando o link do navegador está habilitado, o Visual Studio SignalR atua como um servidor ao qual vários clientes (navegadores) podem se conectar.</span><span class="sxs-lookup"><span data-stu-id="09ae5-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="09ae5-160">O link do navegador também registra um componente de middleware no pipeline de solicitação de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="09ae5-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="09ae5-161">Esse componente injeta referências especiais `<script>` em cada solicitação de página do servidor.</span><span class="sxs-lookup"><span data-stu-id="09ae5-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="09ae5-162">Você pode ver as referências de script selecionando **Exibir origem** no navegador e rolando até o final do conteúdo `<body>` da marca:</span><span class="sxs-lookup"><span data-stu-id="09ae5-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="09ae5-163">Os arquivos de origem não são modificados.</span><span class="sxs-lookup"><span data-stu-id="09ae5-163">Your source files aren't modified.</span></span> <span data-ttu-id="09ae5-164">O componente de middleware injeta as referências de script dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="09ae5-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="09ae5-165">Como o código do lado do navegador é todo o JavaScript, ele funciona em todos SignalR os navegadores que oferecem suporte sem a necessidade de um plug-in de navegador.</span><span class="sxs-lookup"><span data-stu-id="09ae5-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
