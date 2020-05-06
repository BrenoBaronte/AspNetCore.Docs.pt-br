---
title: Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core
author: scottaddie
description: Saiba mais sobre os benefícios de usar os serviços JavaScript para criar um aplicativo de página única (SPA) apoiado por ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/spa-services
ms.openlocfilehash: 65bd5157bb3909f8352debcb1a6dfa7d888eec0e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769917"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="ac99f-103">Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac99f-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="ac99f-104">Por [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="ac99f-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="ac99f-105">Um aplicativo de página única (SPA) é um tipo popular de aplicativo Web devido à sua experiência de usuário sofisticada inerente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="ac99f-106">A integração de estruturas ou bibliotecas SPA do lado do cliente, como [angular](https://angular.io/) ou [reagir](https://facebook.github.io/react/), com estruturas do lado do servidor, como ASP.NET Core pode ser difícil.</span><span class="sxs-lookup"><span data-stu-id="ac99f-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="ac99f-107">Os serviços JavaScript foram desenvolvidos para reduzir o conflito no processo de integração.</span><span class="sxs-lookup"><span data-stu-id="ac99f-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="ac99f-108">Ele permite uma operação direta entre as diferentes pilhas de tecnologia de cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="ac99f-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="ac99f-109">Os recursos descritos neste artigo são obsoletos a partir do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="ac99f-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="ac99f-110">Um mecanismo de integração de estruturas SPA mais simples está disponível no pacote NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) .</span><span class="sxs-lookup"><span data-stu-id="ac99f-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="ac99f-111">Para obter mais informações, consulte [[comunicado] Obsoleting Microsoft. AspNetCore. SpaServices e Microsoft. AspNetCore. nodeservices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="ac99f-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="ac99f-112">O que são os serviços JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac99f-112">What is JavaScript Services</span></span>

<span data-ttu-id="ac99f-113">Os serviços JavaScript são uma coleção de tecnologias do lado do cliente para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac99f-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="ac99f-114">Seu objetivo é posicionar ASP.NET Core como a plataforma preferencial do lado do servidor dos desenvolvedores para a criação de SPAs.</span><span class="sxs-lookup"><span data-stu-id="ac99f-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="ac99f-115">Os serviços JavaScript consistem em dois pacotes do NuGet distintos:</span><span class="sxs-lookup"><span data-stu-id="ac99f-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="ac99f-116">[Microsoft. AspNetCore. nodeservices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (nodeservices)</span><span class="sxs-lookup"><span data-stu-id="ac99f-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="ac99f-117">[Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="ac99f-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="ac99f-118">Esses pacotes são úteis nos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="ac99f-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="ac99f-119">Executar JavaScript no servidor</span><span class="sxs-lookup"><span data-stu-id="ac99f-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="ac99f-120">Usar uma estrutura ou biblioteca de SPA</span><span class="sxs-lookup"><span data-stu-id="ac99f-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="ac99f-121">Criar ativos do lado do cliente com o webpack</span><span class="sxs-lookup"><span data-stu-id="ac99f-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="ac99f-122">Grande parte do foco neste artigo é colocado no uso do pacote SpaServices.</span><span class="sxs-lookup"><span data-stu-id="ac99f-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="ac99f-123">O que é SpaServices</span><span class="sxs-lookup"><span data-stu-id="ac99f-123">What is SpaServices</span></span>

<span data-ttu-id="ac99f-124">O SpaServices foi criado para posicionar ASP.NET Core como a plataforma preferencial do lado do servidor dos desenvolvedores para a criação de SPAs.</span><span class="sxs-lookup"><span data-stu-id="ac99f-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="ac99f-125">SpaServices não é necessário para desenvolver SPAs com ASP.NET Core e não bloqueia os desenvolvedores em uma estrutura de cliente específica.</span><span class="sxs-lookup"><span data-stu-id="ac99f-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="ac99f-126">O SpaServices fornece uma infraestrutura útil, como:</span><span class="sxs-lookup"><span data-stu-id="ac99f-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="ac99f-127">Pré-processamento do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="ac99f-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="ac99f-128">Middleware de dev do webpack</span><span class="sxs-lookup"><span data-stu-id="ac99f-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="ac99f-129">Substituição de módulo quente</span><span class="sxs-lookup"><span data-stu-id="ac99f-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="ac99f-130">Auxiliares de roteamento</span><span class="sxs-lookup"><span data-stu-id="ac99f-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="ac99f-131">Coletivamente, esses componentes de infraestrutura aprimoram o fluxo de trabalho de desenvolvimento e a experiência de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ac99f-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="ac99f-132">Os componentes podem ser adotados individualmente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="ac99f-133">Pré-requisitos para usar o SpaServices</span><span class="sxs-lookup"><span data-stu-id="ac99f-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="ac99f-134">Para trabalhar com o SpaServices, instale o seguinte:</span><span class="sxs-lookup"><span data-stu-id="ac99f-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="ac99f-135">[Node. js](https://nodejs.org/) (versão 6 ou posterior) com NPM</span><span class="sxs-lookup"><span data-stu-id="ac99f-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="ac99f-136">Para verificar se esses componentes estão instalados e podem ser encontrados, execute o seguinte na linha de comando:</span><span class="sxs-lookup"><span data-stu-id="ac99f-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="ac99f-137">Se estiver implantando em um site do Azure, nenhuma ação&mdash;será necessária, o Node. js será instalado e estará disponível nos ambientes de servidor.</span><span class="sxs-lookup"><span data-stu-id="ac99f-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="ac99f-138">No Windows usando o Visual Studio 2017, o SDK é instalado selecionando a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ac99f-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="ac99f-139">Pacote NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)</span><span class="sxs-lookup"><span data-stu-id="ac99f-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="ac99f-140">Pré-processamento do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="ac99f-140">Server-side prerendering</span></span>

<span data-ttu-id="ac99f-141">Um aplicativo universal (também conhecido como isomórficos) é um aplicativo JavaScript capaz de executar ambos no servidor e no cliente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="ac99f-142">Angular, reagir e outras estruturas populares fornecem uma plataforma universal para esse estilo de desenvolvimento de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="ac99f-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="ac99f-143">A ideia é primeiro renderizar os componentes do Framework no servidor por meio do node. js e delegar mais execução ao cliente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="ac99f-144">ASP.NET Core os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) fornecidos pelo SpaServices simplificam a implementação do pré-processamento do lado do servidor invocando as funções JavaScript no servidor.</span><span class="sxs-lookup"><span data-stu-id="ac99f-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="ac99f-145">Pré-requisitos de pré-processamento do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="ac99f-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="ac99f-146">Instale o pacote NPM do [ASPNET-prerenderizando](https://www.npmjs.com/package/aspnet-prerendering) :</span><span class="sxs-lookup"><span data-stu-id="ac99f-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="ac99f-147">Configuração de pré-processamento do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="ac99f-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="ac99f-148">Os auxiliares de marca são descobertos por meio do registro de namespace no arquivo *_ViewImports. cshtml* do projeto:</span><span class="sxs-lookup"><span data-stu-id="ac99f-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="ac99f-149">Esses auxiliares de marcas abstrairam a complexidade da comunicação direta com APIs de nível baixo, aproveitando uma sintaxe semelhante a HTML dentro da exibição do Razor:</span><span class="sxs-lookup"><span data-stu-id="ac99f-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="ac99f-150">Auxiliar de marca do ASP-PreRender-Module</span><span class="sxs-lookup"><span data-stu-id="ac99f-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="ac99f-151">O `asp-prerender-module` auxiliar de marca, usado no exemplo de código anterior, executa *ClientApp/dist/Main-Server. js* no servidor por meio do node. js.</span><span class="sxs-lookup"><span data-stu-id="ac99f-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="ac99f-152">Para fins de clareza, o arquivo *Main-Server. js* é um artefato da tarefa Transpilação do TypeScript para JavaScript no processo de Build do [webpack](https://webpack.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="ac99f-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="ac99f-153">O webpack define um alias de ponto `main-server`de entrada de; e, a passagem do grafo de dependência para esse alias começa no arquivo *ClientApp/boot-Server. TS* :</span><span class="sxs-lookup"><span data-stu-id="ac99f-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="ac99f-154">No exemplo angular a seguir, o arquivo *ClientApp/boot-Server. TS* utiliza a função `createServerRenderer` e `RenderResult` o `aspnet-prerendering` tipo do pacote NPM para configurar a renderização do servidor por meio do node. js.</span><span class="sxs-lookup"><span data-stu-id="ac99f-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="ac99f-155">A marcação HTML destinada à renderização do lado do servidor é passada para uma chamada de função de resolução, que é encapsulada `Promise` em um objeto JavaScript fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="ac99f-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="ac99f-156">O `Promise` significado do objeto é que ele fornece de forma assíncrona a marcação HTML para a página para injeção no elemento PlaceHolder do dom.</span><span class="sxs-lookup"><span data-stu-id="ac99f-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="ac99f-157">ASP-PreRender-auxiliar de marca de dados</span><span class="sxs-lookup"><span data-stu-id="ac99f-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="ac99f-158">Quando combinado com o `asp-prerender-module` auxiliar de marca, o `asp-prerender-data` auxiliar de marca pode ser usado para passar informações contextuais da exibição do Razor para o JavaScript do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="ac99f-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="ac99f-159">Por exemplo, a marcação a seguir passa dados do usuário `main-server` para o módulo:</span><span class="sxs-lookup"><span data-stu-id="ac99f-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="ac99f-160">O argumento `UserName` recebido é serializado usando o serializador JSON interno e é armazenado no `params.data` objeto.</span><span class="sxs-lookup"><span data-stu-id="ac99f-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="ac99f-161">No exemplo angular a seguir, os dados são usados para construir uma saudação personalizada dentro `h1` de um elemento:</span><span class="sxs-lookup"><span data-stu-id="ac99f-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="ac99f-162">Os nomes de propriedade passados em auxiliares de marca são representados com notação de **PascalCase** .</span><span class="sxs-lookup"><span data-stu-id="ac99f-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="ac99f-163">Compare com JavaScript, em que os mesmos nomes de propriedade são representados com **CamelCase**.</span><span class="sxs-lookup"><span data-stu-id="ac99f-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="ac99f-164">A configuração de serialização JSON padrão é responsável por essa diferença.</span><span class="sxs-lookup"><span data-stu-id="ac99f-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="ac99f-165">Para expandir o exemplo de código anterior, os dados podem ser passados do servidor para a exibição por HYDRATING a `globals` Propriedade fornecida para a `resolve` função:</span><span class="sxs-lookup"><span data-stu-id="ac99f-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="ac99f-166">A `postList` matriz definida dentro do `globals` objeto é anexada ao objeto global `window` do navegador.</span><span class="sxs-lookup"><span data-stu-id="ac99f-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="ac99f-167">Essa variável que eleva o escopo global elimina a duplicação do esforço, principalmente, pois ela se refere ao carregamento dos mesmos dados uma vez no servidor e novamente no cliente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![variável de lista de islist global anexada ao objeto Window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="ac99f-169">Middleware de dev do webpack</span><span class="sxs-lookup"><span data-stu-id="ac99f-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="ac99f-170">O [middleware de dev do webpack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) apresenta um fluxo de trabalho de desenvolvimento simplificado no qual o webpack cria recursos sob demanda.</span><span class="sxs-lookup"><span data-stu-id="ac99f-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="ac99f-171">O middleware compila automaticamente e fornece recursos do lado do cliente quando uma página é recarregada no navegador.</span><span class="sxs-lookup"><span data-stu-id="ac99f-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="ac99f-172">A abordagem alternativa é invocar manualmente o webpack por meio do script de Build NPM do projeto quando uma dependência de terceiros ou o código personalizado for alterado.</span><span class="sxs-lookup"><span data-stu-id="ac99f-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="ac99f-173">Um script de Build NPM no arquivo *Package. JSON* é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac99f-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="ac99f-174">Pré-requisitos de middleware de dev do webpack</span><span class="sxs-lookup"><span data-stu-id="ac99f-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="ac99f-175">Instale o pacote [ASPNET-webpack](https://www.npmjs.com/package/aspnet-webpack) NPM:</span><span class="sxs-lookup"><span data-stu-id="ac99f-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="ac99f-176">Configuração de middleware de dev do webpack</span><span class="sxs-lookup"><span data-stu-id="ac99f-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="ac99f-177">O middleware de dev do webpack é registrado no pipeline de solicitação HTTP por meio do seguinte *Startup.cs* código no método `Configure` do arquivo Startup.cs:</span><span class="sxs-lookup"><span data-stu-id="ac99f-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="ac99f-178">O `UseWebpackDevMiddleware` método de extensão deve ser chamado antes de [registrar a hospedagem de arquivo estático](xref:fundamentals/static-files) por meio do método de `UseStaticFiles` extensão.</span><span class="sxs-lookup"><span data-stu-id="ac99f-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="ac99f-179">Por motivos de segurança, registre o middleware somente quando o aplicativo for executado no modo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ac99f-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="ac99f-180">A propriedade do `output.publicPath` arquivo *webpack. config. js* informa ao middleware para observar a `dist` pasta em busca de alterações:</span><span class="sxs-lookup"><span data-stu-id="ac99f-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="ac99f-181">Substituição de módulo quente</span><span class="sxs-lookup"><span data-stu-id="ac99f-181">Hot Module Replacement</span></span>

<span data-ttu-id="ac99f-182">Imagine o recurso de [substituição de módulo quente](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) do webpack como uma evolução do [middleware de desenvolvimento do webpack](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="ac99f-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="ac99f-183">O HMR apresenta todos os mesmos benefícios, mas simplifica ainda mais o fluxo de trabalho de desenvolvimento atualizando automaticamente o conteúdo da página após a compilação das alterações.</span><span class="sxs-lookup"><span data-stu-id="ac99f-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="ac99f-184">Não confunda isso com uma atualização do navegador, que interfere no estado atual da memória e na sessão de depuração do SPA.</span><span class="sxs-lookup"><span data-stu-id="ac99f-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="ac99f-185">Há um link ao vivo entre o serviço de middleware de dev do webpack e o navegador, o que significa que as alterações são enviadas por push para o navegador.</span><span class="sxs-lookup"><span data-stu-id="ac99f-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="ac99f-186">Pré-requisitos de substituição de módulo quente</span><span class="sxs-lookup"><span data-stu-id="ac99f-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="ac99f-187">Instalar o pacote [webpack – Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) NPM:</span><span class="sxs-lookup"><span data-stu-id="ac99f-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="ac99f-188">Configuração de substituição de módulo quente</span><span class="sxs-lookup"><span data-stu-id="ac99f-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="ac99f-189">O componente HMR deve ser registrado no pipeline de solicitação HTTP do MVC no `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="ac99f-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="ac99f-190">Assim como acontece com o [middleware de dev do webpack](#webpack-dev-middleware), o método de `UseWebpackDevMiddleware` extensão deve ser `UseStaticFiles` chamado antes do método de extensão.</span><span class="sxs-lookup"><span data-stu-id="ac99f-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="ac99f-191">Por motivos de segurança, registre o middleware somente quando o aplicativo for executado no modo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ac99f-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="ac99f-192">O arquivo *webpack. config. js* deve definir uma `plugins` matriz, mesmo que ela seja deixada vazia:</span><span class="sxs-lookup"><span data-stu-id="ac99f-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="ac99f-193">Depois de carregar o aplicativo no navegador, a guia Console das ferramentas de desenvolvedor fornece a confirmação da ativação do HMR:</span><span class="sxs-lookup"><span data-stu-id="ac99f-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Mensagem conectada de substituição de módulo quente](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="ac99f-195">Auxiliares de roteamento</span><span class="sxs-lookup"><span data-stu-id="ac99f-195">Routing helpers</span></span>

<span data-ttu-id="ac99f-196">Na maioria dos SPAs baseados em ASP.NET Core, o roteamento do lado do cliente geralmente é desejado, além do roteamento do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="ac99f-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="ac99f-197">Os sistemas de roteamento SPA e MVC podem trabalhar independentemente sem interferência.</span><span class="sxs-lookup"><span data-stu-id="ac99f-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="ac99f-198">Há, no entanto, um caso de borda apresentando desafios: identificar respostas HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="ac99f-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="ac99f-199">Considere o cenário no qual uma rota de extensão do `/some/page` é usada.</span><span class="sxs-lookup"><span data-stu-id="ac99f-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="ac99f-200">Suponha que a solicitação não corresponda por padrão a uma rota do lado do servidor, mas seu padrão corresponde a uma rota do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="ac99f-201">Agora, considere uma solicitação de `/images/user-512.png`entrada para, que geralmente espera encontrar um arquivo de imagem no servidor.</span><span class="sxs-lookup"><span data-stu-id="ac99f-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="ac99f-202">Se esse caminho de recurso solicitado não corresponder a uma rota no lado do servidor ou a um arquivo estático, é improvável que o aplicativo do lado&mdash;do cliente trate normalmente de um código de status http 404.</span><span class="sxs-lookup"><span data-stu-id="ac99f-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="ac99f-203">Pré-requisitos de auxiliares de roteamento</span><span class="sxs-lookup"><span data-stu-id="ac99f-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="ac99f-204">Instale o pacote NPM de roteamento do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="ac99f-205">Usando angular como um exemplo:</span><span class="sxs-lookup"><span data-stu-id="ac99f-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="ac99f-206">Configuração de auxiliares de roteamento</span><span class="sxs-lookup"><span data-stu-id="ac99f-206">Routing helpers configuration</span></span>

<span data-ttu-id="ac99f-207">Um método de extensão `MapSpaFallbackRoute` chamado é usado no `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="ac99f-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="ac99f-208">As rotas são avaliadas na ordem em que estão configuradas.</span><span class="sxs-lookup"><span data-stu-id="ac99f-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="ac99f-209">Consequentemente, `default` a rota no exemplo de código anterior é usada primeiro para correspondência de padrões.</span><span class="sxs-lookup"><span data-stu-id="ac99f-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="ac99f-210">Criar um novo projeto</span><span class="sxs-lookup"><span data-stu-id="ac99f-210">Create a new project</span></span>

<span data-ttu-id="ac99f-211">Os serviços JavaScript fornecem modelos de aplicativo pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="ac99f-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="ac99f-212">O SpaServices é usado nesses modelos em conjunto com estruturas e bibliotecas diferentes, como angular, reagir e Redux.</span><span class="sxs-lookup"><span data-stu-id="ac99f-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="ac99f-213">Esses modelos podem ser instalados por meio do CLI do .NET Core executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ac99f-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="ac99f-214">Uma lista de modelos SPA disponíveis é exibida:</span><span class="sxs-lookup"><span data-stu-id="ac99f-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="ac99f-215">Modelos</span><span class="sxs-lookup"><span data-stu-id="ac99f-215">Templates</span></span>                                 | <span data-ttu-id="ac99f-216">Nome curto</span><span class="sxs-lookup"><span data-stu-id="ac99f-216">Short Name</span></span> | <span data-ttu-id="ac99f-217">Linguagem</span><span class="sxs-lookup"><span data-stu-id="ac99f-217">Language</span></span> | <span data-ttu-id="ac99f-218">Marcas</span><span class="sxs-lookup"><span data-stu-id="ac99f-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="ac99f-219">ASP.NET Core MVC com angular</span><span class="sxs-lookup"><span data-stu-id="ac99f-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="ac99f-220">angular</span><span class="sxs-lookup"><span data-stu-id="ac99f-220">angular</span></span>    | <span data-ttu-id="ac99f-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="ac99f-221">[C#]</span></span>     | <span data-ttu-id="ac99f-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="ac99f-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="ac99f-223">ASP.NET Core MVC com reajam. js</span><span class="sxs-lookup"><span data-stu-id="ac99f-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="ac99f-224">react</span><span class="sxs-lookup"><span data-stu-id="ac99f-224">react</span></span>      | <span data-ttu-id="ac99f-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="ac99f-225">[C#]</span></span>     | <span data-ttu-id="ac99f-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="ac99f-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="ac99f-227">ASP.NET Core MVC com reajam. js e Redux</span><span class="sxs-lookup"><span data-stu-id="ac99f-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="ac99f-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="ac99f-228">reactredux</span></span> | <span data-ttu-id="ac99f-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="ac99f-229">[C#]</span></span>     | <span data-ttu-id="ac99f-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="ac99f-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="ac99f-231">Para criar um novo projeto usando um dos modelos SPA, inclua o **nome curto** do modelo no comando [dotnet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="ac99f-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="ac99f-232">O comando a seguir cria um aplicativo angular com ASP.NET Core MVC configurado para o lado do servidor:</span><span class="sxs-lookup"><span data-stu-id="ac99f-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="ac99f-233">Definir o modo de configuração de tempo de execução</span><span class="sxs-lookup"><span data-stu-id="ac99f-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="ac99f-234">Existem dois modos de configuração de tempo de execução primários:</span><span class="sxs-lookup"><span data-stu-id="ac99f-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="ac99f-235">**Desenvolvimento**:</span><span class="sxs-lookup"><span data-stu-id="ac99f-235">**Development**:</span></span>
  * <span data-ttu-id="ac99f-236">Inclui mapas de origem para facilitar a depuração.</span><span class="sxs-lookup"><span data-stu-id="ac99f-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="ac99f-237">Não otimiza o código do lado do cliente para desempenho.</span><span class="sxs-lookup"><span data-stu-id="ac99f-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="ac99f-238">**Produção**:</span><span class="sxs-lookup"><span data-stu-id="ac99f-238">**Production**:</span></span>
  * <span data-ttu-id="ac99f-239">Exclui mapas de origem.</span><span class="sxs-lookup"><span data-stu-id="ac99f-239">Excludes source maps.</span></span>
  * <span data-ttu-id="ac99f-240">Otimiza o código do lado do cliente por meio de agrupamento e minificação.</span><span class="sxs-lookup"><span data-stu-id="ac99f-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="ac99f-241">ASP.NET Core usa uma variável de ambiente `ASPNETCORE_ENVIRONMENT` chamada para armazenar o modo de configuração.</span><span class="sxs-lookup"><span data-stu-id="ac99f-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="ac99f-242">Para obter mais informações, consulte [definir o ambiente](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="ac99f-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="ac99f-243">Executar com CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ac99f-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="ac99f-244">Restaure os pacotes NuGet e NPM necessários executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="ac99f-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="ac99f-245">Compilar e executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ac99f-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="ac99f-246">O aplicativo é iniciado no localhost de acordo com o [modo de configuração de tempo de execução](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="ac99f-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="ac99f-247">Navegar até `http://localhost:5000` no navegador exibe a página de aterrissagem.</span><span class="sxs-lookup"><span data-stu-id="ac99f-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="ac99f-248">Executar com o Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="ac99f-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="ac99f-249">Abra o arquivo *. csproj* gerado pelo comando [dotnet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="ac99f-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="ac99f-250">Os pacotes NuGet e NPM necessários são restaurados automaticamente após a abertura do projeto.</span><span class="sxs-lookup"><span data-stu-id="ac99f-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="ac99f-251">Esse processo de restauração pode levar alguns minutos e o aplicativo estará pronto para ser executado quando for concluído.</span><span class="sxs-lookup"><span data-stu-id="ac99f-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="ac99f-252">Clique no botão de execução verde ou `Ctrl + F5`pressione e o navegador será aberto na página de aterrissagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac99f-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="ac99f-253">O aplicativo é executado no localhost de acordo com o [modo de configuração de tempo de execução](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="ac99f-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="ac99f-254">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ac99f-254">Test the app</span></span>

<span data-ttu-id="ac99f-255">Os modelos SpaServices são pré-configurados para executar testes no lado do cliente usando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="ac99f-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="ac99f-256">O Jasmine é uma estrutura de teste de unidade popular para JavaScript, enquanto karma é um executor de teste para esses testes.</span><span class="sxs-lookup"><span data-stu-id="ac99f-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="ac99f-257">O karma é configurado para trabalhar com o [middleware de dev do webpack](#webpack-dev-middleware) , de modo que o desenvolvedor não precise parar e executar o teste sempre que forem feitas alterações.</span><span class="sxs-lookup"><span data-stu-id="ac99f-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="ac99f-258">Seja o código em execução no caso de teste ou no próprio caso de teste, o teste é executado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="ac99f-259">Usando o aplicativo angular como um exemplo, dois casos de teste Jasmine já são fornecidos para `CounterComponent` o no arquivo *Counter. Component. spec. TS* :</span><span class="sxs-lookup"><span data-stu-id="ac99f-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="ac99f-260">Abra o prompt de comando no diretório *ClientApp*</span><span class="sxs-lookup"><span data-stu-id="ac99f-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="ac99f-261">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac99f-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="ac99f-262">O script inicia o executor de teste do karma, que lê as configurações definidas no arquivo *karma. conf. js* .</span><span class="sxs-lookup"><span data-stu-id="ac99f-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="ac99f-263">Entre outras configurações, o *karma. conf. js* identifica os arquivos de teste a serem executados por `files` meio de sua matriz:</span><span class="sxs-lookup"><span data-stu-id="ac99f-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="ac99f-264">Publicar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ac99f-264">Publish the app</span></span>

<span data-ttu-id="ac99f-265">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) para obter mais informações sobre como publicar no Azure.</span><span class="sxs-lookup"><span data-stu-id="ac99f-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="ac99f-266">Combinar os ativos gerados do lado do cliente e os artefatos ASP.NET Core publicados em um pacote pronto para implantação pode ser trabalhoso.</span><span class="sxs-lookup"><span data-stu-id="ac99f-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="ac99f-267">Felizmente, o SpaServices orquestra todo o processo de publicação com um destino do MSBuild personalizado `RunWebpack`chamado:</span><span class="sxs-lookup"><span data-stu-id="ac99f-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="ac99f-268">O destino do MSBuild tem as seguintes responsabilidades:</span><span class="sxs-lookup"><span data-stu-id="ac99f-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="ac99f-269">Restaure os pacotes NPM.</span><span class="sxs-lookup"><span data-stu-id="ac99f-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="ac99f-270">Crie um Build de nível de produção dos ativos de terceiros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="ac99f-271">Crie uma compilação de nível de produção dos ativos personalizados do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="ac99f-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="ac99f-272">Copie os ativos gerados pelo webpack para a pasta de publicação.</span><span class="sxs-lookup"><span data-stu-id="ac99f-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="ac99f-273">O destino do MSBuild é invocado ao executar:</span><span class="sxs-lookup"><span data-stu-id="ac99f-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="ac99f-274">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ac99f-274">Additional resources</span></span>

* [<span data-ttu-id="ac99f-275">Documentos angulares</span><span class="sxs-lookup"><span data-stu-id="ac99f-275">Angular Docs</span></span>](https://angular.io/docs)
