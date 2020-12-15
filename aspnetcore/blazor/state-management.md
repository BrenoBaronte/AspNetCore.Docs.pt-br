---
title: BlazorGerenciamento de estado ASP.NET Core
author: guardrex
description: Saiba como persistir o estado em Blazor Server aplicativos.
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 6e6f3047da30490caff4f820003a3018e8c26aaa
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506599"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="0ea87-103">BlazorGerenciamento de estado ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ea87-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="0ea87-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0ea87-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="0ea87-105">O estado do usuário criado em um Blazor WebAssembly aplicativo é mantido na memória do navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="0ea87-106">Exemplos de estado do usuário mantido na memória do navegador incluem:</span><span class="sxs-lookup"><span data-stu-id="0ea87-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="0ea87-107">A hierarquia de instâncias de componente e sua saída de renderização mais recente na interface do usuário renderizada.</span><span class="sxs-lookup"><span data-stu-id="0ea87-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="0ea87-108">Os valores de campos e propriedades em instâncias de componente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="0ea87-109">Dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="0ea87-110">Os valores definidos por meio de chamadas de [interoperabilidade do JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="0ea87-111">Quando um usuário fecha e abre novamente o navegador ou recarrega a página, o estado do usuário mantido na memória do navegador é perdido.</span><span class="sxs-lookup"><span data-stu-id="0ea87-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

> [!NOTE]
> <span data-ttu-id="0ea87-112">O [armazenamento de navegador protegido](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) ( <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> namespace) depende do ASP.NET Core proteção de dados e só tem suporte para Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0ea87-112">[Protected Browser Storage](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> namespace) relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="0ea87-113">Persistir o estado entre as sessões do navegador</span><span class="sxs-lookup"><span data-stu-id="0ea87-113">Persist state across browser sessions</span></span>

<span data-ttu-id="0ea87-114">Em geral, mantenha o estado entre as sessões de navegador onde os usuários estão criando dados ativamente, não simplesmente lendo dados já existentes.</span><span class="sxs-lookup"><span data-stu-id="0ea87-114">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="0ea87-115">Para preservar o estado entre as sessões do navegador, o aplicativo deve manter os dados em algum outro local de armazenamento que a memória do navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-115">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="0ea87-116">A persistência de estado não é automática.</span><span class="sxs-lookup"><span data-stu-id="0ea87-116">State persistence isn't automatic.</span></span> <span data-ttu-id="0ea87-117">Você deve executar etapas ao desenvolver o aplicativo para implementar a persistência de dados com estado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-117">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="0ea87-118">A persistência de dados normalmente é necessária apenas para o estado de alto valor que os usuários gastaram esforços para criar.</span><span class="sxs-lookup"><span data-stu-id="0ea87-118">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="0ea87-119">Nos exemplos a seguir, o estado de persistência economiza tempo ou ajuda em atividades comerciais:</span><span class="sxs-lookup"><span data-stu-id="0ea87-119">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="0ea87-120">Web Forms de várias etapas: é demorado para um usuário reinserir dados para várias etapas concluídas de um formulário da Web de várias etapas se seu estado for perdido.</span><span class="sxs-lookup"><span data-stu-id="0ea87-120">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="0ea87-121">Um usuário perde o estado nesse cenário se ele navega para fora do formulário e retorna mais tarde.</span><span class="sxs-lookup"><span data-stu-id="0ea87-121">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="0ea87-122">Carrinhos de compras: qualquer componente comercialmente importante de um aplicativo que representa a possível receita pode ser mantido.</span><span class="sxs-lookup"><span data-stu-id="0ea87-122">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="0ea87-123">Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando eles retornam para o site mais tarde.</span><span class="sxs-lookup"><span data-stu-id="0ea87-123">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="0ea87-124">Um aplicativo só pode persistir o *estado do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="0ea87-124">An app can only persist *app state*.</span></span> <span data-ttu-id="0ea87-125">As UIs não podem ser persistentes, como instâncias de componente e suas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="0ea87-125">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="0ea87-126">Os componentes e as árvores de renderização geralmente não são serializáveis.</span><span class="sxs-lookup"><span data-stu-id="0ea87-126">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="0ea87-127">Para persistir o estado da interface do usuário, como os nós expandidos de um controle de exibição de árvore, o aplicativo deve usar o código personalizado para modelar o comportamento do estado da interface do usuário como estado do aplicativo serializável.</span><span class="sxs-lookup"><span data-stu-id="0ea87-127">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="0ea87-128">Onde persistir o estado</span><span class="sxs-lookup"><span data-stu-id="0ea87-128">Where to persist state</span></span>

<span data-ttu-id="0ea87-129">Existem localizações comuns para o estado persistente:</span><span class="sxs-lookup"><span data-stu-id="0ea87-129">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="0ea87-130">Armazenamento no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="0ea87-130">Server-side storage</span></span>](#server-side-storage-wasm)
* [<span data-ttu-id="0ea87-131">URL</span><span class="sxs-lookup"><span data-stu-id="0ea87-131">URL</span></span>](#url-wasm)
* [<span data-ttu-id="0ea87-132">Armazenamento de navegador</span><span class="sxs-lookup"><span data-stu-id="0ea87-132">Browser storage</span></span>](#browser-storage-wasm)
* [<span data-ttu-id="0ea87-133">Serviço de contêiner de estado na memória</span><span class="sxs-lookup"><span data-stu-id="0ea87-133">In-memory state container service</span></span>](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm"><span data-ttu-id="0ea87-134">Armazenamento no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="0ea87-134">Server-side storage</span></span></h2>

<span data-ttu-id="0ea87-135">Para persistência de dados permanente que abrange vários usuários e dispositivos, o aplicativo pode usar armazenamento independente do lado do servidor acessado por meio de uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="0ea87-135">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="0ea87-136">As opções incluem:</span><span class="sxs-lookup"><span data-stu-id="0ea87-136">Options include:</span></span>

* <span data-ttu-id="0ea87-137">Armazenamento de blob</span><span class="sxs-lookup"><span data-stu-id="0ea87-137">Blob storage</span></span>
* <span data-ttu-id="0ea87-138">Armazenamento de chave-valor</span><span class="sxs-lookup"><span data-stu-id="0ea87-138">Key-value storage</span></span>
* <span data-ttu-id="0ea87-139">Banco de dados relacional</span><span class="sxs-lookup"><span data-stu-id="0ea87-139">Relational database</span></span>
* <span data-ttu-id="0ea87-140">Armazenamento de tabela</span><span class="sxs-lookup"><span data-stu-id="0ea87-140">Table storage</span></span>

<span data-ttu-id="0ea87-141">Depois que os dados são salvos, o estado do usuário é mantido e está disponível em qualquer nova sessão do navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-141">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="0ea87-142">Como Blazor WebAssembly os aplicativos são executados inteiramente no navegador do usuário, eles exigem medidas adicionais para acessar sistemas externos seguros, como bancos de dados e serviços de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-142">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="0ea87-143">Blazor WebAssembly os aplicativos são protegidos da mesma maneira que os aplicativos de página única (SPAs).</span><span class="sxs-lookup"><span data-stu-id="0ea87-143">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="0ea87-144">Normalmente, um aplicativo autentica um usuário por meio [](https://oauth.net) / [do OAuth OpenID Connect (OIDC)](https://openid.net/connect/) e, em seguida, interage com os serviços de armazenamento e bancos de dados por meio de chamadas à API da Web para um aplicativo do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="0ea87-144">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="0ea87-145">O aplicativo do lado do servidor Media a transferência de dados entre o Blazor WebAssembly aplicativo e o serviço ou banco de dados do armazenamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-145">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="0ea87-146">O Blazor WebAssembly aplicativo mantém uma conexão efêmera com o aplicativo do lado do servidor, enquanto o aplicativo do lado do servidor tem uma conexão persistente com o armazenamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-146">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="0ea87-147">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="0ea87-147">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="0ea87-148">Blazor*Segurança e Identity* los</span><span class="sxs-lookup"><span data-stu-id="0ea87-148">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="0ea87-149">Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte o seguinte:</span><span class="sxs-lookup"><span data-stu-id="0ea87-149">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="0ea87-150">Bancos de dados do Azure</span><span class="sxs-lookup"><span data-stu-id="0ea87-150">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="0ea87-151">Documentação do armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="0ea87-151">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-wasm"><span data-ttu-id="0ea87-152">URL</span><span class="sxs-lookup"><span data-stu-id="0ea87-152">URL</span></span></h2>

<span data-ttu-id="0ea87-153">Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="0ea87-153">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="0ea87-154">Exemplos de estado de usuário modelados na URL incluem:</span><span class="sxs-lookup"><span data-stu-id="0ea87-154">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="0ea87-155">A ID de uma entidade exibida.</span><span class="sxs-lookup"><span data-stu-id="0ea87-155">The ID of a viewed entity.</span></span>
* <span data-ttu-id="0ea87-156">O número da página atual em uma grade paginável.</span><span class="sxs-lookup"><span data-stu-id="0ea87-156">The current page number in a paged grid.</span></span>

<span data-ttu-id="0ea87-157">O conteúdo da barra de endereços do navegador será retido se o usuário recarregar a página manualmente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-157">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="0ea87-158">Para obter informações sobre como definir padrões de URL com a [`@page`](xref:mvc/views/razor#page) diretiva, consulte <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="0ea87-158">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-wasm"><span data-ttu-id="0ea87-159">Armazenamento de navegador</span><span class="sxs-lookup"><span data-stu-id="0ea87-159">Browser storage</span></span></h2>

<span data-ttu-id="0ea87-160">Para dados transitórios que o usuário está criando ativamente, um local de armazenamento usado normalmente é o navegador [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) e as [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) coleções:</span><span class="sxs-lookup"><span data-stu-id="0ea87-160">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="0ea87-161">`localStorage` está no escopo da janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-161">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="0ea87-162">Se o usuário recarregar a página ou fechar e abrir novamente o navegador, o estado persistirá.</span><span class="sxs-lookup"><span data-stu-id="0ea87-162">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="0ea87-163">Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias.</span><span class="sxs-lookup"><span data-stu-id="0ea87-163">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="0ea87-164">Os dados persistem `localStorage` até serem explicitamente desmarcados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-164">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="0ea87-165">`sessionStorage` está no escopo da guia navegador. Se o usuário recarregar a guia, o estado persiste.</span><span class="sxs-lookup"><span data-stu-id="0ea87-165">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="0ea87-166">Se o usuário fechar a guia ou o navegador, o estado será perdido.</span><span class="sxs-lookup"><span data-stu-id="0ea87-166">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="0ea87-167">Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-167">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="0ea87-168">`localStorage` e `sessionStorage` pode ser usado em Blazor WebAssembly aplicativos, mas apenas escrevendo código personalizado ou usando um pacote de terceiros.</span><span class="sxs-lookup"><span data-stu-id="0ea87-168">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="0ea87-169">Em geral, `sessionStorage` é mais seguro usar.</span><span class="sxs-lookup"><span data-stu-id="0ea87-169">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="0ea87-170">`sessionStorage` evita o risco de um usuário abrir várias guias e encontrar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="0ea87-170">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="0ea87-171">Bugs no armazenamento de estado entre guias.</span><span class="sxs-lookup"><span data-stu-id="0ea87-171">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="0ea87-172">Comportamento confuso quando uma guia substitui o estado de outras guias.</span><span class="sxs-lookup"><span data-stu-id="0ea87-172">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="0ea87-173">`localStorage` é a melhor opção se o aplicativo precisar manter o estado entre fechar e abrir novamente o navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-173">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="0ea87-174">Os usuários podem exibir ou adulterar os dados armazenados no `localStorage` e no `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="0ea87-174">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

<h2 id="in-memory-state-container-service-wasm"><span data-ttu-id="0ea87-175">Serviço de contêiner de estado na memória</span><span class="sxs-lookup"><span data-stu-id="0ea87-175">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="0ea87-176">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0ea87-176">Additional resources</span></span>

* [<span data-ttu-id="0ea87-177">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="0ea87-177">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="0ea87-178">Blazor Server é uma estrutura de aplicativo com estado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-178">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="0ea87-179">Na maioria das vezes, o aplicativo mantém uma conexão com o servidor.</span><span class="sxs-lookup"><span data-stu-id="0ea87-179">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="0ea87-180">O estado do usuário é mantido na memória do servidor em um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="0ea87-180">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="0ea87-181">Exemplos de estado do usuário mantido em um circuito incluem:</span><span class="sxs-lookup"><span data-stu-id="0ea87-181">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="0ea87-182">A hierarquia de instâncias de componente e sua saída de renderização mais recente na interface do usuário renderizada.</span><span class="sxs-lookup"><span data-stu-id="0ea87-182">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="0ea87-183">Os valores de campos e propriedades em instâncias de componente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-183">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="0ea87-184">Dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.</span><span class="sxs-lookup"><span data-stu-id="0ea87-184">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="0ea87-185">O estado do usuário também pode ser encontrado em variáveis JavaScript no conjunto de memória do navegador por meio de chamadas de [interoperabilidade do JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-185">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="0ea87-186">Se um usuário sofrer uma perda de conexão de rede temporária, Blazor o tentará reconectar o usuário ao seu circuito original com seu estado original.</span><span class="sxs-lookup"><span data-stu-id="0ea87-186">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="0ea87-187">No entanto, a reconexão de um usuário ao seu circuito original na memória do servidor nem sempre é possível:</span><span class="sxs-lookup"><span data-stu-id="0ea87-187">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="0ea87-188">O servidor não pode manter um circuito desconectado para sempre.</span><span class="sxs-lookup"><span data-stu-id="0ea87-188">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="0ea87-189">O servidor deve liberar um circuito desconectado após um tempo limite ou quando o servidor está sob pressão de memória.</span><span class="sxs-lookup"><span data-stu-id="0ea87-189">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="0ea87-190">Em ambientes de implantação com balanceamento de carga e vários servidores, os servidores individuais podem falhar ou ser removidos automaticamente quando não forem mais necessários para lidar com o volume geral de solicitações.</span><span class="sxs-lookup"><span data-stu-id="0ea87-190">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="0ea87-191">O servidor original solicitações de processamento de um usuário pode ficar indisponível quando o usuário tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="0ea87-191">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="0ea87-192">O usuário pode fechar e reabrir o navegador ou recarregar a página, o que remove qualquer estado mantido na memória do navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-192">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="0ea87-193">Por exemplo, os valores de variáveis JavaScript definidos por meio de chamadas de interoperabilidade JavaScript são perdidos.</span><span class="sxs-lookup"><span data-stu-id="0ea87-193">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="0ea87-194">Quando um usuário não pode ser reconectado ao seu circuito original, o usuário recebe um novo circuito com um estado vazio.</span><span class="sxs-lookup"><span data-stu-id="0ea87-194">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="0ea87-195">Isso é equivalente a fechar e reabrir um aplicativo de área de trabalho.</span><span class="sxs-lookup"><span data-stu-id="0ea87-195">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="0ea87-196">Persistir estado entre circuitos</span><span class="sxs-lookup"><span data-stu-id="0ea87-196">Persist state across circuits</span></span>

<span data-ttu-id="0ea87-197">Em geral, mantenha o estado entre circuitos em que os usuários estão criando dados ativamente, não simplesmente lendo dados já existentes.</span><span class="sxs-lookup"><span data-stu-id="0ea87-197">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="0ea87-198">Para preservar o estado entre circuitos, o aplicativo deve manter os dados em algum outro local de armazenamento que a memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="0ea87-198">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="0ea87-199">A persistência de estado não é automática.</span><span class="sxs-lookup"><span data-stu-id="0ea87-199">State persistence isn't automatic.</span></span> <span data-ttu-id="0ea87-200">Você deve executar etapas ao desenvolver o aplicativo para implementar a persistência de dados com estado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-200">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="0ea87-201">A persistência de dados normalmente é necessária apenas para o estado de alto valor que os usuários gastaram esforços para criar.</span><span class="sxs-lookup"><span data-stu-id="0ea87-201">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="0ea87-202">Nos exemplos a seguir, o estado de persistência economiza tempo ou ajuda em atividades comerciais:</span><span class="sxs-lookup"><span data-stu-id="0ea87-202">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="0ea87-203">Web Forms de várias etapas: é demorado para um usuário reinserir dados para várias etapas concluídas de um formulário da Web de várias etapas se seu estado for perdido.</span><span class="sxs-lookup"><span data-stu-id="0ea87-203">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="0ea87-204">Um usuário perde o estado nesse cenário se ele navega para fora do formulário e retorna mais tarde.</span><span class="sxs-lookup"><span data-stu-id="0ea87-204">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="0ea87-205">Carrinhos de compras: qualquer componente comercialmente importante de um aplicativo que representa a possível receita pode ser mantido.</span><span class="sxs-lookup"><span data-stu-id="0ea87-205">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="0ea87-206">Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando eles retornam para o site mais tarde.</span><span class="sxs-lookup"><span data-stu-id="0ea87-206">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="0ea87-207">Um aplicativo só pode persistir o *estado do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="0ea87-207">An app can only persist *app state*.</span></span> <span data-ttu-id="0ea87-208">As UIs não podem ser persistentes, como instâncias de componente e suas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="0ea87-208">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="0ea87-209">Os componentes e as árvores de renderização geralmente não são serializáveis.</span><span class="sxs-lookup"><span data-stu-id="0ea87-209">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="0ea87-210">Para persistir o estado da interface do usuário, como os nós expandidos de um controle de exibição de árvore, o aplicativo deve usar o código personalizado para modelar o comportamento do estado da interface do usuário como estado do aplicativo serializável.</span><span class="sxs-lookup"><span data-stu-id="0ea87-210">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="0ea87-211">Onde persistir o estado</span><span class="sxs-lookup"><span data-stu-id="0ea87-211">Where to persist state</span></span>

<span data-ttu-id="0ea87-212">Existem localizações comuns para o estado persistente:</span><span class="sxs-lookup"><span data-stu-id="0ea87-212">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="0ea87-213">Armazenamento no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="0ea87-213">Server-side storage</span></span>](#server-side-storage-server)
* [<span data-ttu-id="0ea87-214">URL</span><span class="sxs-lookup"><span data-stu-id="0ea87-214">URL</span></span>](#url-server)
* [<span data-ttu-id="0ea87-215">Armazenamento de navegador</span><span class="sxs-lookup"><span data-stu-id="0ea87-215">Browser storage</span></span>](#browser-storage-server)
* [<span data-ttu-id="0ea87-216">Serviço de contêiner de estado na memória</span><span class="sxs-lookup"><span data-stu-id="0ea87-216">In-memory state container service</span></span>](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server"><span data-ttu-id="0ea87-217">Armazenamento no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="0ea87-217">Server-side storage</span></span></h2>

<span data-ttu-id="0ea87-218">Para persistência de dados permanente que abrange vários usuários e dispositivos, o aplicativo pode usar o armazenamento do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="0ea87-218">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="0ea87-219">As opções incluem:</span><span class="sxs-lookup"><span data-stu-id="0ea87-219">Options include:</span></span>

* <span data-ttu-id="0ea87-220">Armazenamento de blob</span><span class="sxs-lookup"><span data-stu-id="0ea87-220">Blob storage</span></span>
* <span data-ttu-id="0ea87-221">Armazenamento de chave-valor</span><span class="sxs-lookup"><span data-stu-id="0ea87-221">Key-value storage</span></span>
* <span data-ttu-id="0ea87-222">Banco de dados relacional</span><span class="sxs-lookup"><span data-stu-id="0ea87-222">Relational database</span></span>
* <span data-ttu-id="0ea87-223">Armazenamento de tabela</span><span class="sxs-lookup"><span data-stu-id="0ea87-223">Table storage</span></span>

<span data-ttu-id="0ea87-224">Depois que os dados são salvos, o estado do usuário é mantido e está disponível em qualquer circuito novo.</span><span class="sxs-lookup"><span data-stu-id="0ea87-224">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="0ea87-225">Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte o seguinte:</span><span class="sxs-lookup"><span data-stu-id="0ea87-225">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="0ea87-226">Bancos de dados do Azure</span><span class="sxs-lookup"><span data-stu-id="0ea87-226">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="0ea87-227">Documentação do armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="0ea87-227">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-server"><span data-ttu-id="0ea87-228">URL</span><span class="sxs-lookup"><span data-stu-id="0ea87-228">URL</span></span></h2>

<span data-ttu-id="0ea87-229">Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="0ea87-229">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="0ea87-230">Exemplos de estado de usuário modelados na URL incluem:</span><span class="sxs-lookup"><span data-stu-id="0ea87-230">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="0ea87-231">A ID de uma entidade exibida.</span><span class="sxs-lookup"><span data-stu-id="0ea87-231">The ID of a viewed entity.</span></span>
* <span data-ttu-id="0ea87-232">O número da página atual em uma grade paginável.</span><span class="sxs-lookup"><span data-stu-id="0ea87-232">The current page number in a paged grid.</span></span>

<span data-ttu-id="0ea87-233">O conteúdo da barra de endereços do navegador é mantido:</span><span class="sxs-lookup"><span data-stu-id="0ea87-233">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="0ea87-234">Se o usuário recarregar a página manualmente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-234">If the user manually reloads the page.</span></span>
* <span data-ttu-id="0ea87-235">Se o servidor Web ficar indisponível e o usuário for forçado a recarregar a página a fim de se conectar a um servidor diferente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-235">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="0ea87-236">Para obter informações sobre como definir padrões de URL com a [`@page`](xref:mvc/views/razor#page) diretiva, consulte <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="0ea87-236">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-server"><span data-ttu-id="0ea87-237">Armazenamento de navegador</span><span class="sxs-lookup"><span data-stu-id="0ea87-237">Browser storage</span></span></h2>

<span data-ttu-id="0ea87-238">Para dados transitórios que o usuário está criando ativamente, um local de armazenamento usado normalmente é o navegador [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) e as [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) coleções:</span><span class="sxs-lookup"><span data-stu-id="0ea87-238">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="0ea87-239">`localStorage` está no escopo da janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-239">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="0ea87-240">Se o usuário recarregar a página ou fechar e abrir novamente o navegador, o estado persistirá.</span><span class="sxs-lookup"><span data-stu-id="0ea87-240">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="0ea87-241">Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias.</span><span class="sxs-lookup"><span data-stu-id="0ea87-241">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="0ea87-242">Os dados persistem `localStorage` até serem explicitamente desmarcados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-242">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="0ea87-243">`sessionStorage` está no escopo da guia navegador. Se o usuário recarregar a guia, o estado persiste.</span><span class="sxs-lookup"><span data-stu-id="0ea87-243">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="0ea87-244">Se o usuário fechar a guia ou o navegador, o estado será perdido.</span><span class="sxs-lookup"><span data-stu-id="0ea87-244">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="0ea87-245">Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-245">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="0ea87-246">Em geral, `sessionStorage` é mais seguro usar.</span><span class="sxs-lookup"><span data-stu-id="0ea87-246">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="0ea87-247">`sessionStorage` evita o risco de um usuário abrir várias guias e encontrar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="0ea87-247">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="0ea87-248">Bugs no armazenamento de estado entre guias.</span><span class="sxs-lookup"><span data-stu-id="0ea87-248">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="0ea87-249">Comportamento confuso quando uma guia substitui o estado de outras guias.</span><span class="sxs-lookup"><span data-stu-id="0ea87-249">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="0ea87-250">`localStorage` é a melhor opção se o aplicativo precisar manter o estado entre fechar e abrir novamente o navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-250">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="0ea87-251">Advertências para usar o armazenamento de navegador:</span><span class="sxs-lookup"><span data-stu-id="0ea87-251">Caveats for using browser storage:</span></span>

* <span data-ttu-id="0ea87-252">Assim como o uso de um banco de dados do lado do servidor, o carregamento e o salvamento do data são assíncronos.</span><span class="sxs-lookup"><span data-stu-id="0ea87-252">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="0ea87-253">Ao contrário de um banco de dados do lado do servidor, o armazenamento não está disponível durante o pré-processamento porque a página solicitada não existe no navegador durante o estágio de pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-253">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="0ea87-254">O armazenamento de alguns kilobytes de dados é razoável para persistir para Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0ea87-254">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="0ea87-255">Além de alguns quilobytes, você deve considerar as implicações de desempenho porque os dados são carregados e salvos na rede.</span><span class="sxs-lookup"><span data-stu-id="0ea87-255">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="0ea87-256">Os usuários podem exibir ou adulterar os dados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-256">Users may view or tamper with the data.</span></span> <span data-ttu-id="0ea87-257">[ASP.NET Core proteção de dados](xref:security/data-protection/introduction) pode reduzir o risco.</span><span class="sxs-lookup"><span data-stu-id="0ea87-257">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="0ea87-258">Por exemplo, [ASP.NET Core armazenamento de navegador protegido](#aspnet-core-protected-browser-storage) usa a proteção de dados do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ea87-258">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="0ea87-259">Os pacotes NuGet de terceiros fornecem APIs para trabalhar com o `localStorage` e o `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="0ea87-259">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="0ea87-260">Vale a pena considerar a escolha de um pacote que use de modo transparente [ASP.NET Core proteção de dados](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="0ea87-260">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0ea87-261">A proteção de dados criptografa os dados armazenados e reduz o risco potencial de adulterar os dados armazenados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-261">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="0ea87-262">Se os dados serializados em JSON forem armazenados em texto sem formatação, os usuários poderão ver os dados usando as ferramentas de desenvolvedor do navegador e também modificar os dados armazenados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-262">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="0ea87-263">Proteger os dados nem sempre é um problema porque os dados podem ser triviais por natureza.</span><span class="sxs-lookup"><span data-stu-id="0ea87-263">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="0ea87-264">Por exemplo, ler ou modificar a cor armazenada de um elemento de interface do usuário não é um risco de segurança significativo para o usuário ou a organização.</span><span class="sxs-lookup"><span data-stu-id="0ea87-264">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="0ea87-265">Evite permitir que os usuários inspecionem ou violem *dados confidenciais*.</span><span class="sxs-lookup"><span data-stu-id="0ea87-265">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="0ea87-266">ASP.NET Core o armazenamento de navegador protegido</span><span class="sxs-lookup"><span data-stu-id="0ea87-266">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="0ea87-267">ASP.NET Core armazenamento de navegador protegido aproveita a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) para o e o [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-267">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="0ea87-268">O armazenamento de navegador protegido depende do ASP.NET Core proteção de dados e tem suporte apenas para Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0ea87-268">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="0ea87-269">Salvar e carregar dados dentro de um componente</span><span class="sxs-lookup"><span data-stu-id="0ea87-269">Save and load data within a component</span></span>

<span data-ttu-id="0ea87-270">Em qualquer componente que exija carregar ou salvar dados no armazenamento de navegador, use a [`@inject`](xref:mvc/views/razor#inject) diretiva para injetar uma instância de um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="0ea87-270">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="0ea87-271">A escolha depende de qual local de armazenamento do navegador você deseja usar.</span><span class="sxs-lookup"><span data-stu-id="0ea87-271">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="0ea87-272">No exemplo a seguir, `sessionStorage` é usado:</span><span class="sxs-lookup"><span data-stu-id="0ea87-272">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="0ea87-273">A `@using` diretiva pode ser colocada no arquivo do aplicativo `_Imports.razor` , em vez de no componente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-273">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="0ea87-274">O uso do `_Imports.razor` arquivo disponibiliza o namespace para segmentos maiores do aplicativo ou todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ea87-274">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="0ea87-275">Para persistir o `currentCount` valor no `Counter` componente de um aplicativo com base no Blazor Server modelo de projeto, modifique o `IncrementCount` método a ser usado `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="0ea87-275">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="0ea87-276">Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável.</span><span class="sxs-lookup"><span data-stu-id="0ea87-276">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="0ea87-277">Os aplicativos têm maior probabilidade de armazenar objetos de modelo inteiros que incluem estado complexo.</span><span class="sxs-lookup"><span data-stu-id="0ea87-277">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="0ea87-278">`ProtectedSessionStore` o serializa e desserializa automaticamente os dados JSON para armazenar objetos de estado complexo.</span><span class="sxs-lookup"><span data-stu-id="0ea87-278">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="0ea87-279">No exemplo de código anterior, os `currentCount` dados são armazenados como `sessionStorage['count']` no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="0ea87-279">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="0ea87-280">Os dados não são armazenados em texto sem formatação, mas, em vez disso, são protegidos usando ASP.NET Core proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-280">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="0ea87-281">Os dados criptografados poderão ser inspecionados se `sessionStorage['count']` o for avaliado no console do desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-281">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="0ea87-282">Para recuperar os `currentCount` dados, se o usuário retornar ao `Counter` componente posteriormente, incluindo se o usuário está em um novo circuito, use `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="0ea87-282">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="0ea87-283">Se os parâmetros do componente incluírem o estado de navegação, chame `ProtectedSessionStore.GetAsync` e atribua um não `null` resultado <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , não <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="0ea87-283">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="0ea87-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> é chamado apenas uma vez quando o componente é instanciado pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="0ea87-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="0ea87-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Não será chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página.</span><span class="sxs-lookup"><span data-stu-id="0ea87-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="0ea87-286">Para obter mais informações, consulte <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="0ea87-286">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="0ea87-287">Os exemplos nesta seção só funcionarão se o servidor não tiver o pré-processamento habilitado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-287">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="0ea87-288">Com o pré-processamento habilitado, um erro é gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-288">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="0ea87-289">Desabilite o pré-processamento ou adicione código adicional para trabalhar com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-289">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="0ea87-290">Para saber mais sobre como escrever código que funciona com o pré-processamento, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-290">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="0ea87-291">Manipular o estado de carregamento</span><span class="sxs-lookup"><span data-stu-id="0ea87-291">Handle the loading state</span></span>

<span data-ttu-id="0ea87-292">Como o armazenamento de navegador é acessado de forma assíncrona em uma conexão de rede, sempre há um período de tempo antes que os dados sejam carregados e disponibilizados para um componente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-292">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="0ea87-293">Para obter os melhores resultados, processe uma mensagem de estado de carregamento durante o carregamento em andamento, em vez de exibir dados em branco ou padrão.</span><span class="sxs-lookup"><span data-stu-id="0ea87-293">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="0ea87-294">Uma abordagem é controlar se os dados são `null` , o que significa que os dados ainda estão sendo carregados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-294">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="0ea87-295">No componente padrão `Counter` , a contagem é mantida em um `int` .</span><span class="sxs-lookup"><span data-stu-id="0ea87-295">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="0ea87-296">[Tornar `currentCount` anulável](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) adicionando um ponto de interrogação ( `?` ) ao tipo ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="0ea87-296">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="0ea87-297">Em vez de exibir incondicionalmente a contagem e o **`Increment`** botão, exiba esses elementos somente se os dados forem carregados por meio da verificação <xref:System.Nullable%601.HasValue%2A> :</span><span class="sxs-lookup"><span data-stu-id="0ea87-297">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="0ea87-298">Processar pré-processamento</span><span class="sxs-lookup"><span data-stu-id="0ea87-298">Handle prerendering</span></span>

<span data-ttu-id="0ea87-299">Durante o pré-processamento:</span><span class="sxs-lookup"><span data-stu-id="0ea87-299">During prerendering:</span></span>

* <span data-ttu-id="0ea87-300">Não existe uma conexão interativa com o navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="0ea87-300">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="0ea87-301">O navegador ainda não tem uma página na qual possa executar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0ea87-301">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="0ea87-302">`localStorage` ou `sessionStorage` não estão disponíveis durante o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-302">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="0ea87-303">Se o componente tentar interagir com o armazenamento, um erro será gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-303">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="0ea87-304">Uma maneira de resolver o erro é desabilitar o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-304">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="0ea87-305">Normalmente, essa é a melhor opção se o aplicativo fizer uso intensivo de armazenamento baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-305">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="0ea87-306">O pré-processamento adiciona complexidade e não beneficia o aplicativo porque o aplicativo não pode colocar nenhum conteúdo útil até `localStorage` ou `sessionStorage` estar disponível.</span><span class="sxs-lookup"><span data-stu-id="0ea87-306">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="0ea87-307">Para desabilitar o pré-processamento, abra o `Pages/_Host.cshtml` arquivo e altere o `render-mode` atributo do auxiliar de [marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="0ea87-307">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="0ea87-308">O pré-processamento pode ser útil para outras páginas que não usam o `localStorage` ou o `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="0ea87-308">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="0ea87-309">Para reter o pré-processamento, adie a operação de carregamento até que o navegador esteja conectado ao circuito.</span><span class="sxs-lookup"><span data-stu-id="0ea87-309">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="0ea87-310">Veja a seguir um exemplo de como armazenar um valor de contador:</span><span class="sxs-lookup"><span data-stu-id="0ea87-310">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="0ea87-311">Fatorar a preservação do estado para um local comum</span><span class="sxs-lookup"><span data-stu-id="0ea87-311">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="0ea87-312">Se muitos componentes dependem do armazenamento baseado em navegador, a nova implementação do código do provedor de estado muitas vezes cria a duplicação de código.</span><span class="sxs-lookup"><span data-stu-id="0ea87-312">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="0ea87-313">Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-313">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="0ea87-314">Os componentes filho podem trabalhar com dados persistentes sem considerar o mecanismo de persistência de estado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-314">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="0ea87-315">No exemplo a seguir de um `CounterStateProvider` componente, os dados do contador são persistidos para `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="0ea87-315">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="0ea87-316">O `CounterStateProvider` componente manipula a fase de carregamento não processando seu conteúdo filho até que o carregamento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="0ea87-316">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="0ea87-317">Para usar o `CounterStateProvider` componente, empacote uma instância do componente em qualquer outro componente que exija acesso ao estado do contador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-317">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="0ea87-318">Para tornar o estado acessível a todos os componentes em um aplicativo, empacote o `CounterStateProvider` componente em volta do <xref:Microsoft.AspNetCore.Components.Routing.Router> no `App` componente ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="0ea87-318">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="0ea87-319">Os componentes encapsulados recebem e podem modificar o estado do contador persistente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-319">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="0ea87-320">O componente a seguir `Counter` implementa o padrão:</span><span class="sxs-lookup"><span data-stu-id="0ea87-320">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="0ea87-321">O componente anterior não precisa interagir com o `ProtectedBrowserStorage` , nem lidar com uma fase de "carregamento".</span><span class="sxs-lookup"><span data-stu-id="0ea87-321">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="0ea87-322">Para lidar com o pré-processamento conforme descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consumam os dados do contador funcionem automaticamente com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-322">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="0ea87-323">Para obter mais informações, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-323">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="0ea87-324">Em geral, o padrão de *componente pai do provedor de estado* é recomendado:</span><span class="sxs-lookup"><span data-stu-id="0ea87-324">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="0ea87-325">Para consumir o estado em vários componentes.</span><span class="sxs-lookup"><span data-stu-id="0ea87-325">To consume state across many components.</span></span>
* <span data-ttu-id="0ea87-326">Se houver apenas um objeto de estado de nível superior para persistir.</span><span class="sxs-lookup"><span data-stu-id="0ea87-326">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="0ea87-327">Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em locais diferentes, é melhor evitar o estado de persistência globalmente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-327">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="0ea87-328">Pacote NuGet experimental do armazenamento de navegador protegido</span><span class="sxs-lookup"><span data-stu-id="0ea87-328">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="0ea87-329">ASP.NET Core armazenamento de navegador protegido aproveita a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) para o e o [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-329">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="0ea87-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` é um pacote experimental sem suporte não adequado para uso em produção.</span><span class="sxs-lookup"><span data-stu-id="0ea87-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="0ea87-331">O pacote só está disponível para uso em aplicativos ASP.NET Core 3,1 Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="0ea87-331">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="0ea87-332">Configuração</span><span class="sxs-lookup"><span data-stu-id="0ea87-332">Configuration</span></span>

1. <span data-ttu-id="0ea87-333">Adicione uma referência de pacote a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-333">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="0ea87-334">No `Pages/_Host.cshtml` arquivo, adicione o seguinte script dentro da marca de fechamento `</body>` :</span><span class="sxs-lookup"><span data-stu-id="0ea87-334">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="0ea87-335">No `Startup.ConfigureServices` , chame `AddProtectedBrowserStorage` para adicionar `localStorage` e `sessionStorage` serviços à coleção de serviços:</span><span class="sxs-lookup"><span data-stu-id="0ea87-335">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="0ea87-336">Salvar e carregar dados dentro de um componente</span><span class="sxs-lookup"><span data-stu-id="0ea87-336">Save and load data within a component</span></span>

<span data-ttu-id="0ea87-337">Em qualquer componente que exija carregar ou salvar dados no armazenamento de navegador, use a [`@inject`](xref:mvc/views/razor#inject) diretiva para injetar uma instância de um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="0ea87-337">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="0ea87-338">A escolha depende de qual local de armazenamento do navegador você deseja usar.</span><span class="sxs-lookup"><span data-stu-id="0ea87-338">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="0ea87-339">No exemplo a seguir, `sessionStorage` é usado:</span><span class="sxs-lookup"><span data-stu-id="0ea87-339">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="0ea87-340">A `@using` instrução pode ser colocada em um `_Imports.razor` arquivo em vez de no componente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-340">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="0ea87-341">O uso do `_Imports.razor` arquivo disponibiliza o namespace para segmentos maiores do aplicativo ou todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ea87-341">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="0ea87-342">Para persistir o `currentCount` valor no `Counter` componente de um aplicativo com base no Blazor Server modelo de projeto, modifique o `IncrementCount` método a ser usado `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="0ea87-342">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="0ea87-343">Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável.</span><span class="sxs-lookup"><span data-stu-id="0ea87-343">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="0ea87-344">Os aplicativos têm maior probabilidade de armazenar objetos de modelo inteiros que incluem estado complexo.</span><span class="sxs-lookup"><span data-stu-id="0ea87-344">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="0ea87-345">`ProtectedSessionStore` o serializa e desserializa automaticamente os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="0ea87-345">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="0ea87-346">No exemplo de código anterior, os `currentCount` dados são armazenados como `sessionStorage['count']` no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="0ea87-346">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="0ea87-347">Os dados não são armazenados em texto sem formatação, mas, em vez disso, são protegidos usando ASP.NET Core proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-347">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="0ea87-348">Os dados criptografados poderão ser inspecionados se `sessionStorage['count']` o for avaliado no console do desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-348">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="0ea87-349">Para recuperar os `currentCount` dados, se o usuário retornar ao `Counter` componente posteriormente, incluindo se eles estão em um circuito totalmente novo, use `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="0ea87-349">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="0ea87-350">Se os parâmetros do componente incluírem o estado de navegação, chame `ProtectedSessionStore.GetAsync` e atribua o resultado em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , não <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="0ea87-350">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="0ea87-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> é chamado apenas uma vez quando o componente é instanciado pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="0ea87-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="0ea87-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Não será chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página.</span><span class="sxs-lookup"><span data-stu-id="0ea87-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="0ea87-353">Para obter mais informações, consulte <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="0ea87-353">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="0ea87-354">Os exemplos nesta seção só funcionarão se o servidor não tiver o pré-processamento habilitado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-354">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="0ea87-355">Com o pré-processamento habilitado, um erro é gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-355">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="0ea87-356">Desabilite o pré-processamento ou adicione código adicional para trabalhar com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-356">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="0ea87-357">Para saber mais sobre como escrever código que funciona com o pré-processamento, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-357">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="0ea87-358">Manipular o estado de carregamento</span><span class="sxs-lookup"><span data-stu-id="0ea87-358">Handle the loading state</span></span>

<span data-ttu-id="0ea87-359">Como o armazenamento de navegador é acessado de forma assíncrona em uma conexão de rede, sempre há um período de tempo antes que os dados sejam carregados e disponibilizados para um componente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-359">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="0ea87-360">Para obter os melhores resultados, processe uma mensagem de estado de carregamento durante o carregamento em andamento, em vez de exibir dados em branco ou padrão.</span><span class="sxs-lookup"><span data-stu-id="0ea87-360">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="0ea87-361">Uma abordagem é controlar se os dados são `null` , o que significa que os dados ainda estão sendo carregados.</span><span class="sxs-lookup"><span data-stu-id="0ea87-361">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="0ea87-362">No componente padrão `Counter` , a contagem é mantida em um `int` .</span><span class="sxs-lookup"><span data-stu-id="0ea87-362">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="0ea87-363">[Tornar `currentCount` anulável](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) adicionando um ponto de interrogação ( `?` ) ao tipo ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="0ea87-363">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="0ea87-364">Em vez de exibir incondicionalmente a contagem e o **`Increment`** botão, escolha exibir esses elementos somente se os dados forem carregados:</span><span class="sxs-lookup"><span data-stu-id="0ea87-364">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="0ea87-365">Processar pré-processamento</span><span class="sxs-lookup"><span data-stu-id="0ea87-365">Handle prerendering</span></span>

<span data-ttu-id="0ea87-366">Durante o pré-processamento:</span><span class="sxs-lookup"><span data-stu-id="0ea87-366">During prerendering:</span></span>

* <span data-ttu-id="0ea87-367">Não existe uma conexão interativa com o navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="0ea87-367">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="0ea87-368">O navegador ainda não tem uma página na qual possa executar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0ea87-368">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="0ea87-369">`localStorage` ou `sessionStorage` não estão disponíveis durante o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-369">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="0ea87-370">Se o componente tentar interagir com o armazenamento, um erro será gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-370">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="0ea87-371">Uma maneira de resolver o erro é desabilitar o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-371">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="0ea87-372">Normalmente, essa é a melhor opção se o aplicativo fizer uso intensivo de armazenamento baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-372">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="0ea87-373">O pré-processamento adiciona complexidade e não beneficia o aplicativo porque o aplicativo não pode colocar nenhum conteúdo útil até `localStorage` ou `sessionStorage` estar disponível.</span><span class="sxs-lookup"><span data-stu-id="0ea87-373">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="0ea87-374">Para desabilitar o pré-processamento, abra o `Pages/_Host.cshtml` arquivo e altere o `render-mode` atributo do auxiliar de [marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="0ea87-374">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="0ea87-375">O pré-processamento pode ser útil para outras páginas que não usam o `localStorage` ou o `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="0ea87-375">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="0ea87-376">Para reter o pré-processamento, adie a operação de carregamento até que o navegador esteja conectado ao circuito.</span><span class="sxs-lookup"><span data-stu-id="0ea87-376">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="0ea87-377">Veja a seguir um exemplo de como armazenar um valor de contador:</span><span class="sxs-lookup"><span data-stu-id="0ea87-377">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="0ea87-378">Fatorar a preservação do estado para um local comum</span><span class="sxs-lookup"><span data-stu-id="0ea87-378">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="0ea87-379">Se muitos componentes dependem do armazenamento baseado em navegador, a nova implementação do código do provedor de estado muitas vezes cria a duplicação de código.</span><span class="sxs-lookup"><span data-stu-id="0ea87-379">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="0ea87-380">Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-380">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="0ea87-381">Os componentes filho podem trabalhar com dados persistentes sem considerar o mecanismo de persistência de estado.</span><span class="sxs-lookup"><span data-stu-id="0ea87-381">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="0ea87-382">No exemplo a seguir de um `CounterStateProvider` componente, os dados do contador são persistidos para `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="0ea87-382">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="0ea87-383">O `CounterStateProvider` componente manipula a fase de carregamento não processando seu conteúdo filho até que o carregamento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="0ea87-383">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="0ea87-384">Para usar o `CounterStateProvider` componente, empacote uma instância do componente em qualquer outro componente que exija acesso ao estado do contador.</span><span class="sxs-lookup"><span data-stu-id="0ea87-384">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="0ea87-385">Para tornar o estado acessível a todos os componentes em um aplicativo, empacote o `CounterStateProvider` componente em volta do <xref:Microsoft.AspNetCore.Components.Routing.Router> no `App` componente ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="0ea87-385">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="0ea87-386">Os componentes encapsulados recebem e podem modificar o estado do contador persistente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-386">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="0ea87-387">O componente a seguir `Counter` implementa o padrão:</span><span class="sxs-lookup"><span data-stu-id="0ea87-387">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="0ea87-388">O componente anterior não precisa interagir com o `ProtectedBrowserStorage` , nem lidar com uma fase de "carregamento".</span><span class="sxs-lookup"><span data-stu-id="0ea87-388">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="0ea87-389">Para lidar com o pré-processamento conforme descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consumam os dados do contador funcionem automaticamente com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="0ea87-389">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="0ea87-390">Para obter mais informações, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="0ea87-390">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="0ea87-391">Em geral, o padrão de *componente pai do provedor de estado* é recomendado:</span><span class="sxs-lookup"><span data-stu-id="0ea87-391">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="0ea87-392">Para consumir o estado em vários componentes.</span><span class="sxs-lookup"><span data-stu-id="0ea87-392">To consume state across many components.</span></span>
* <span data-ttu-id="0ea87-393">Se houver apenas um objeto de estado de nível superior para persistir.</span><span class="sxs-lookup"><span data-stu-id="0ea87-393">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="0ea87-394">Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em locais diferentes, é melhor evitar o estado de persistência globalmente.</span><span class="sxs-lookup"><span data-stu-id="0ea87-394">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

<h2 id="in-memory-state-container-service-server"><span data-ttu-id="0ea87-395">Serviço de contêiner de estado na memória</span><span class="sxs-lookup"><span data-stu-id="0ea87-395">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
