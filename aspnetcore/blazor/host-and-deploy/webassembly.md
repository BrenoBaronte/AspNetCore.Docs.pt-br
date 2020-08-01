---
title: Hospedar e implantar ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 15c5f02043a83e499eb5ec36fda52171124fe202
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87443980"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="2f183-103">Hospedar e implantar ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2f183-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="2f183-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)e [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="2f183-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="2f183-105">Com o [ Blazor WebAssembly modelo de hospedagem](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="2f183-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="2f183-106">O Blazor aplicativo, suas dependências e o tempo de execução do .NET são baixados para o navegador em paralelo.</span><span class="sxs-lookup"><span data-stu-id="2f183-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="2f183-107">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="2f183-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="2f183-108">Há suporte para as seguintes estratégias de implantação:</span><span class="sxs-lookup"><span data-stu-id="2f183-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="2f183-109">O Blazor aplicativo é servido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f183-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="2f183-110">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="2f183-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="2f183-111">O Blazor aplicativo é colocado em um servidor Web ou serviço de hospedagem estática, em que o .net não é usado para servir o Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f183-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="2f183-112">Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um Blazor WebAssembly aplicativo como um subaplicativo do IIS.</span><span class="sxs-lookup"><span data-stu-id="2f183-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="2f183-113">Compactação</span><span class="sxs-lookup"><span data-stu-id="2f183-113">Compression</span></span>

<span data-ttu-id="2f183-114">Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="2f183-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="2f183-115">Os seguintes algoritmos de compactação são usados:</span><span class="sxs-lookup"><span data-stu-id="2f183-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="2f183-116">[Brotli](https://tools.ietf.org/html/rfc7932) (nível mais alto)</span><span class="sxs-lookup"><span data-stu-id="2f183-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="2f183-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="2f183-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="2f183-118">Blazoro se baseia no host para o fornecer os arquivos compactados apropriados.</span><span class="sxs-lookup"><span data-stu-id="2f183-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="2f183-119">Ao usar um projeto ASP.NET Core hospedado, o projeto host é capaz de executar a negociação de conteúdo e fornecer os arquivos compactados estaticamente.</span><span class="sxs-lookup"><span data-stu-id="2f183-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="2f183-120">Ao hospedar um Blazor WebAssembly aplicativo autônomo, um trabalho adicional pode ser necessário para garantir que arquivos compactados estaticamente sejam atendidos:</span><span class="sxs-lookup"><span data-stu-id="2f183-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="2f183-121">Para `web.config` a configuração de compactação do IIS, consulte a seção [IIS: Brotli e a compactação Gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="2f183-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="2f183-122">Ao hospedar soluções de hospedagem estática que não dão suporte à negociação de conteúdo de arquivo compactado estaticamente, como páginas do GitHub, considere configurar o aplicativo para buscar e decodificar arquivos compactados Brotli:</span><span class="sxs-lookup"><span data-stu-id="2f183-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="2f183-123">Obtenha o decodificador Brotli do JavaScript do [repositório GitHub do Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="2f183-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="2f183-124">A partir de julho de 2020, o arquivo de decodificador é nomeado `decode.min.js` e encontrado na [ `js` pasta](https://github.com/google/brotli/tree/master/js)do repositório.</span><span class="sxs-lookup"><span data-stu-id="2f183-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="2f183-125">Atualize o aplicativo para usar o decodificador.</span><span class="sxs-lookup"><span data-stu-id="2f183-125">Update the app to use the decoder.</span></span> <span data-ttu-id="2f183-126">Altere a marcação dentro da marca de fechamento `<body>` `wwwroot/index.html` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="2f183-126">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
<span data-ttu-id="2f183-127">Para desabilitar a compactação, adicione a `BlazorEnableCompression` Propriedade MSBuild ao arquivo de projeto do aplicativo e defina o valor como `false` :</span><span class="sxs-lookup"><span data-stu-id="2f183-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="2f183-128">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="2f183-128">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="2f183-129">O roteamento de solicitações para componentes de página em um Blazor WebAssembly aplicativo não é tão simples quanto o roteamento de solicitações em um Blazor Server aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="2f183-129">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="2f183-130">Considere um Blazor WebAssembly aplicativo com dois componentes:</span><span class="sxs-lookup"><span data-stu-id="2f183-130">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="2f183-131">`Main.razor`: Carrega na raiz do aplicativo e contém um link para o `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="2f183-131">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="2f183-132">`About.razor`: `About` componente.</span><span class="sxs-lookup"><span data-stu-id="2f183-132">`About.razor`: `About` component.</span></span>

<span data-ttu-id="2f183-133">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="2f183-133">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="2f183-134">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="2f183-134">The browser makes a request.</span></span>
1. <span data-ttu-id="2f183-135">A página padrão é retornada, o que geralmente é `index.html` .</span><span class="sxs-lookup"><span data-stu-id="2f183-135">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="2f183-136">`index.html`Inicializa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f183-136">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="2f183-137">Blazoro roteador do é carregado e o Razor `Main` componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="2f183-137">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="2f183-138">Na página principal, selecionar o link para o `About` componente funciona no cliente, pois o Blazor roteador interrompe o navegador de fazer uma solicitação na Internet para `www.contoso.com` `About` e serve o componente renderizado em `About` si.</span><span class="sxs-lookup"><span data-stu-id="2f183-138">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="2f183-139">Todas as solicitações de pontos de extremidade internos *no Blazor WebAssembly aplicativo* funcionam da mesma maneira: as solicitações não disparam solicitações baseadas em navegador para recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="2f183-139">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="2f183-140">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="2f183-140">The router handles the requests internally.</span></span>

<span data-ttu-id="2f183-141">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="2f183-141">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="2f183-142">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="2f183-142">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="2f183-143">Como os navegadores fazem solicitações para hosts baseados na Internet para páginas do lado do cliente, servidores Web e serviços de hospedagem devem regravar todas as solicitações de recursos não fisicamente no servidor para a `index.html` página.</span><span class="sxs-lookup"><span data-stu-id="2f183-143">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="2f183-144">Quando `index.html` é retornado, o roteador do aplicativo Blazor assume o failover e responde com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="2f183-144">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="2f183-145">Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo publicado do aplicativo `web.config` .</span><span class="sxs-lookup"><span data-stu-id="2f183-145">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="2f183-146">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="2f183-146">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="2f183-147">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f183-147">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="2f183-148">Uma *implantação hospedada* serve o Blazor WebAssembly aplicativo para os navegadores de um [aplicativo ASP.NET Core](xref:index) executado em um servidor Web.</span><span class="sxs-lookup"><span data-stu-id="2f183-148">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="2f183-149">O Blazor WebAssembly aplicativo cliente é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="2f183-149">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="2f183-150">Os dois aplicativos são implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="2f183-150">The two apps are deployed together.</span></span> <span data-ttu-id="2f183-151">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f183-151">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="2f183-152">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto de \*\* Blazor WebAssembly aplicativo\*\* ( `blazorwasm` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) com a **`Hosted`** opção selecionada ( `-ho|--hosted` ao usar o `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="2f183-152">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="2f183-153">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="2f183-153">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="2f183-154">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="2f183-154">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="2f183-155">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="2f183-155">Standalone deployment</span></span>

<span data-ttu-id="2f183-156">Uma *implantação autônoma* serve o Blazor WebAssembly aplicativo como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="2f183-156">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="2f183-157">Qualquer servidor de arquivos estático é capaz de atender ao Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f183-157">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="2f183-158">Os ativos de implantação autônomo são publicados na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta.</span><span class="sxs-lookup"><span data-stu-id="2f183-158">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="2f183-159">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="2f183-159">Azure App Service</span></span>

<span data-ttu-id="2f183-160">Blazor WebAssemblyos aplicativos podem ser implantados em serviços Azure App no Windows, que hospedam o aplicativo no [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="2f183-160">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="2f183-161">Blazor WebAssemblyAtualmente, não há suporte para a implantação de um aplicativo autônomo no serviço Azure app para Linux.</span><span class="sxs-lookup"><span data-stu-id="2f183-161">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="2f183-162">Uma imagem do servidor Linux para hospedar o aplicativo não está disponível no momento.</span><span class="sxs-lookup"><span data-stu-id="2f183-162">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="2f183-163">O trabalho está em andamento para habilitar esse cenário.</span><span class="sxs-lookup"><span data-stu-id="2f183-163">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="2f183-164">IIS</span><span class="sxs-lookup"><span data-stu-id="2f183-164">IIS</span></span>

<span data-ttu-id="2f183-165">O IIS é um servidor de arquivos estático com capacidade para Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="2f183-165">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="2f183-166">Para configurar o IIS para hospedar Blazor , consulte [criar um site estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="2f183-166">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="2f183-167">Os ativos publicados são criados na `/bin/Release/{TARGET FRAMEWORK}/publish` pasta.</span><span class="sxs-lookup"><span data-stu-id="2f183-167">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="2f183-168">Hospede o conteúdo da `publish` pasta no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f183-168">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="2f183-169">web.config</span><span class="sxs-lookup"><span data-stu-id="2f183-169">web.config</span></span>

<span data-ttu-id="2f183-170">Quando um Blazor projeto é publicado, um `web.config` arquivo é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="2f183-170">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="2f183-171">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="2f183-171">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="2f183-172">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="2f183-172">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="2f183-173">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="2f183-173">`.json`: `application/json`</span></span>
  * <span data-ttu-id="2f183-174">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="2f183-174">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="2f183-175">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2f183-175">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="2f183-176">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2f183-176">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="2f183-177">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="2f183-177">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="2f183-178">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="2f183-178">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="2f183-179">Serve o subdiretório onde residem os ativos estáticos do aplicativo ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="2f183-179">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="2f183-180">Crie o roteamento de fallback de SPA para que as solicitações de ativos que não sejam de arquivo sejam redirecionadas para o documento padrão do aplicativo em sua pasta de ativos estáticos ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="2f183-180">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="2f183-181">Usar um web.config personalizado</span><span class="sxs-lookup"><span data-stu-id="2f183-181">Use a custom web.config</span></span>

<span data-ttu-id="2f183-182">Para usar um `web.config` arquivo personalizado, coloque o `web.config` arquivo personalizado na raiz da pasta do projeto e publique o projeto.</span><span class="sxs-lookup"><span data-stu-id="2f183-182">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="2f183-183">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="2f183-183">Install the URL Rewrite Module</span></span>

<span data-ttu-id="2f183-184">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="2f183-184">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="2f183-185">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="2f183-185">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="2f183-186">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="2f183-186">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="2f183-187">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="2f183-187">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="2f183-188">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="2f183-188">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="2f183-189">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="2f183-189">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="2f183-190">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="2f183-190">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="2f183-191">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="2f183-191">Copy the installer to the server.</span></span> <span data-ttu-id="2f183-192">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="2f183-192">Run the installer.</span></span> <span data-ttu-id="2f183-193">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="2f183-193">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="2f183-194">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="2f183-194">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="2f183-195">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="2f183-195">Configure the website</span></span>

<span data-ttu-id="2f183-196">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f183-196">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="2f183-197">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="2f183-197">The folder contains:</span></span>

* <span data-ttu-id="2f183-198">O `web.config` arquivo que o IIS usa para configurar o site, incluindo as regras de redirecionamento necessárias e os tipos de conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="2f183-198">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="2f183-199">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f183-199">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="2f183-200">Hospedar como um subaplicativo do IIS</span><span class="sxs-lookup"><span data-stu-id="2f183-200">Host as an IIS sub-app</span></span>

<span data-ttu-id="2f183-201">Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="2f183-201">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="2f183-202">Desabilite o manipulador do módulo ASP.NET Core herdado.</span><span class="sxs-lookup"><span data-stu-id="2f183-202">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="2f183-203">Remova o manipulador no Blazor arquivo publicado do aplicativo `web.config` adicionando uma `<handlers>` seção ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="2f183-203">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="2f183-204">Desabilite a herança da seção do aplicativo raiz (pai) `<system.webServer>` usando um `<location>` elemento com `inheritInChildApplications` definido como `false` :</span><span class="sxs-lookup"><span data-stu-id="2f183-204">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="2f183-205">A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="2f183-205">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="2f183-206">Defina o caminho base do aplicativo no arquivo do aplicativo `index.html` como o alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="2f183-206">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="2f183-207">Compactação Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="2f183-207">Brotli and Gzip compression</span></span>

<span data-ttu-id="2f183-208">O IIS pode ser configurado via `web.config` para servir ativos compactados Brotli ou gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="2f183-208">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="2f183-209">Para obter um exemplo de configuração, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="2f183-209">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="2f183-210">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="2f183-210">Troubleshooting</span></span>

<span data-ttu-id="2f183-211">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="2f183-211">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="2f183-212">Quando o módulo não está instalado, o `web.config` arquivo não pode ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="2f183-212">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="2f183-213">Isso impede que o Gerenciador do IIS carregue a configuração do site e o site de serviços de Blazor arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="2f183-213">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="2f183-214">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="2f183-214">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="2f183-215">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="2f183-215">Azure Storage</span></span>

<span data-ttu-id="2f183-216">A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos sem servidor Blazor .</span><span class="sxs-lookup"><span data-stu-id="2f183-216">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="2f183-217">Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="2f183-217">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="2f183-218">Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:</span><span class="sxs-lookup"><span data-stu-id="2f183-218">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="2f183-219">Defina o **Nome do documento de índice** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="2f183-219">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="2f183-220">Defina o **Caminho do documento de erro** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="2f183-220">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="2f183-221">Razoros componentes e outros pontos de extremidade que não são de arquivo não residem em caminhos físicos no conteúdo estático armazenado pelo serviço BLOB.</span><span class="sxs-lookup"><span data-stu-id="2f183-221">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="2f183-222">Quando uma solicitação para um desses recursos é recebida e o Blazor roteador deve lidar, o erro *404-não encontrado* gerado pelo serviço blob roteia a solicitação para o caminho do **documento de erro**.</span><span class="sxs-lookup"><span data-stu-id="2f183-222">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="2f183-223">O `index.html` blob é retornado e o Blazor roteador carrega e processa o caminho.</span><span class="sxs-lookup"><span data-stu-id="2f183-223">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="2f183-224">Se os arquivos não forem carregados no tempo de execução devido a tipos MIME inadequados nos cabeçalhos dos arquivos `Content-Type` , execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="2f183-224">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="2f183-225">Configure suas ferramentas para definir os tipos MIME corretos ( `Content-Type` cabeçalhos) quando os arquivos são implantados.</span><span class="sxs-lookup"><span data-stu-id="2f183-225">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="2f183-226">Altere os tipos MIME ( `Content-Type` cabeçalhos) para os arquivos depois que o aplicativo for implantado.</span><span class="sxs-lookup"><span data-stu-id="2f183-226">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="2f183-227">Em Gerenciador de Armazenamento (portal do Azure) para cada arquivo:</span><span class="sxs-lookup"><span data-stu-id="2f183-227">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="2f183-228">Clique com o botão direito do mouse no arquivo e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="2f183-228">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="2f183-229">Defina o **ContentType** e selecione o botão **salvar** .</span><span class="sxs-lookup"><span data-stu-id="2f183-229">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="2f183-230">Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="2f183-230">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="2f183-231">Nginx</span><span class="sxs-lookup"><span data-stu-id="2f183-231">Nginx</span></span>

<span data-ttu-id="2f183-232">O arquivo a seguir `nginx.conf` é simplificado para mostrar como configurar o Nginx para enviar o `index.html` arquivo sempre que ele não encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="2f183-232">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="2f183-233">Ao definir o [limite de taxa de intermitência Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) com [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , os Blazor WebAssembly aplicativos podem exigir um grande `burst` valor de parâmetro para acomodar o número relativamente grande de solicitações feitas por um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f183-233">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="2f183-234">Inicialmente, defina o valor para pelo menos 60:</span><span class="sxs-lookup"><span data-stu-id="2f183-234">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="2f183-235">Aumente o valor se as ferramentas de desenvolvedor do navegador ou uma ferramenta de tráfego de rede indicar que as solicitações estão recebendo um código de status de *503-Serviço indisponível* .</span><span class="sxs-lookup"><span data-stu-id="2f183-235">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="2f183-236">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="2f183-236">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="2f183-237">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="2f183-237">Nginx in Docker</span></span>

<span data-ttu-id="2f183-238">Para hospedar Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem Nginx baseada em Alpine.</span><span class="sxs-lookup"><span data-stu-id="2f183-238">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="2f183-239">Atualize o Dockerfile para copiar o `nginx.config` arquivo para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="2f183-239">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="2f183-240">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2f183-240">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="2f183-241">Apache</span><span class="sxs-lookup"><span data-stu-id="2f183-241">Apache</span></span>

<span data-ttu-id="2f183-242">Para implantar um Blazor WebAssembly aplicativo no CentOS 7 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="2f183-242">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="2f183-243">Crie o arquivo de configuração do Apache.</span><span class="sxs-lookup"><span data-stu-id="2f183-243">Create the Apache configuration file.</span></span> <span data-ttu-id="2f183-244">O exemplo a seguir é um arquivo de configuração simplificado ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="2f183-244">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="2f183-245">Coloque o arquivo de configuração do Apache no `/etc/httpd/conf.d/` diretório, que é o diretório de configuração padrão do Apache no CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="2f183-245">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="2f183-246">Coloque os arquivos do aplicativo no `/var/www/blazorapp` diretório (o local especificado para `DocumentRoot` o no arquivo de configuração).</span><span class="sxs-lookup"><span data-stu-id="2f183-246">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="2f183-247">Reinicie o serviço apache.</span><span class="sxs-lookup"><span data-stu-id="2f183-247">Restart the Apache service.</span></span>

<span data-ttu-id="2f183-248">Para obter mais informações, consulte [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="2f183-248">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="2f183-249">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="2f183-249">GitHub Pages</span></span>

<span data-ttu-id="2f183-250">Para tratar as regravações de URL, adicione um `404.html` arquivo com um script que manipula o redirecionamento da solicitação para a `index.html` página.</span><span class="sxs-lookup"><span data-stu-id="2f183-250">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="2f183-251">Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="2f183-251">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="2f183-252">Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="2f183-252">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="2f183-253">Ao usar um site do projeto em vez de um site da organização, adicione ou atualize a `<base>` marca no `index.html` .</span><span class="sxs-lookup"><span data-stu-id="2f183-253">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="2f183-254">Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="2f183-254">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="2f183-255">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="2f183-255">Host configuration values</span></span>

<span data-ttu-id="2f183-256">os [ Blazor WebAssembly aplicativos](xref:blazor/hosting-models#blazor-webassembly) podem aceitar os seguintes valores de configuração de host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2f183-256">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="2f183-257">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="2f183-257">Content root</span></span>

<span data-ttu-id="2f183-258">O `--contentroot` argumento define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo ([raiz de conteúdo](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="2f183-258">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="2f183-259">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f183-259">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="2f183-260">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="2f183-260">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2f183-261">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="2f183-261">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="2f183-262">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="2f183-262">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="2f183-263">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2f183-263">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="2f183-264">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="2f183-264">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2f183-265">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="2f183-265">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="2f183-266">Caminho base</span><span class="sxs-lookup"><span data-stu-id="2f183-266">Path base</span></span>

<span data-ttu-id="2f183-267">O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz (a `<base>` marca `href` é definida para um caminho diferente `/` de para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="2f183-267">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="2f183-268">Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f183-268">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="2f183-269">Para obter mais informações, consulte [caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="2f183-269">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2f183-270">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="2f183-270">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="2f183-271">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="2f183-271">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="2f183-272">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="2f183-272">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2f183-273">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="2f183-273">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="2f183-274">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="2f183-274">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="2f183-275">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2f183-275">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="2f183-276">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="2f183-276">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2f183-277">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="2f183-277">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="2f183-278">URLs</span><span class="sxs-lookup"><span data-stu-id="2f183-278">URLs</span></span>

<span data-ttu-id="2f183-279">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="2f183-279">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="2f183-280">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="2f183-280">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2f183-281">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="2f183-281">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="2f183-282">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="2f183-282">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="2f183-283">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2f183-283">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="2f183-284">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="2f183-284">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2f183-285">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="2f183-285">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="2f183-286">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="2f183-286">Configure the Linker</span></span>

<span data-ttu-id="2f183-287">Blazorexecuta a vinculação de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="2f183-287">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="2f183-288">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="2f183-288">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="2f183-289">Carregamento de recurso de inicialização personalizada</span><span class="sxs-lookup"><span data-stu-id="2f183-289">Custom boot resource loading</span></span>

<span data-ttu-id="2f183-290">Um Blazor WebAssembly aplicativo pode ser inicializado com a `loadBootResource` função para substituir o mecanismo de carregamento do recurso de inicialização interno.</span><span class="sxs-lookup"><span data-stu-id="2f183-290">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="2f183-291">Use `loadBootResource` para os seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="2f183-291">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="2f183-292">Permitir que os usuários carreguem recursos estáticos, como dados de fuso horário ou `dotnet.wasm` de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="2f183-292">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="2f183-293">Carregue assemblies compactados usando uma solicitação HTTP e descompacte-os no cliente para hosts que não dão suporte à busca de conteúdo compactado do servidor.</span><span class="sxs-lookup"><span data-stu-id="2f183-293">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="2f183-294">Recursos de alias para um nome diferente redirecionando cada `fetch` solicitação para um novo nome.</span><span class="sxs-lookup"><span data-stu-id="2f183-294">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="2f183-295">`loadBootResource`os parâmetros aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2f183-295">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="2f183-296">Parâmetro</span><span class="sxs-lookup"><span data-stu-id="2f183-296">Parameter</span></span>    | <span data-ttu-id="2f183-297">Descrição</span><span class="sxs-lookup"><span data-stu-id="2f183-297">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="2f183-298">Tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="2f183-298">The type of the resource.</span></span> <span data-ttu-id="2f183-299">Tipos permissiváveis: `assembly` ,, `pdb` , `dotnetjs` `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="2f183-299">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="2f183-300">O nome do recurso.</span><span class="sxs-lookup"><span data-stu-id="2f183-300">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="2f183-301">O URI relativo ou absoluto do recurso.</span><span class="sxs-lookup"><span data-stu-id="2f183-301">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="2f183-302">A cadeia de caracteres de integridade que representa o conteúdo esperado na resposta.</span><span class="sxs-lookup"><span data-stu-id="2f183-302">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="2f183-303">`loadBootResource`retorna qualquer um dos seguintes para substituir o processo de carregamento:</span><span class="sxs-lookup"><span data-stu-id="2f183-303">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="2f183-304">Cadeia de caracteres de URI.</span><span class="sxs-lookup"><span data-stu-id="2f183-304">URI string.</span></span> <span data-ttu-id="2f183-305">No exemplo a seguir ( `wwwroot/index.html` ), os seguintes arquivos são atendidos de uma CDN em `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="2f183-305">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="2f183-306">Dados de fuso horário</span><span class="sxs-lookup"><span data-stu-id="2f183-306">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="2f183-307">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="2f183-307">`Promise<Response>`.</span></span> <span data-ttu-id="2f183-308">Passe o `integrity` parâmetro em um cabeçalho para manter o comportamento de verificação de integridade padrão.</span><span class="sxs-lookup"><span data-stu-id="2f183-308">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="2f183-309">O exemplo a seguir ( `wwwroot/index.html` ) adiciona um cabeçalho HTTP personalizado às solicitações de saída e passa o `integrity` parâmetro para a `fetch` chamada:</span><span class="sxs-lookup"><span data-stu-id="2f183-309">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="2f183-310">`null`/`undefined`, o que resulta no comportamento de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="2f183-310">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="2f183-311">As fontes externas devem retornar os cabeçalhos CORS necessários para que os navegadores permitam o carregamento de recursos entre origens.</span><span class="sxs-lookup"><span data-stu-id="2f183-311">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="2f183-312">O CDNs geralmente fornece os cabeçalhos necessários por padrão.</span><span class="sxs-lookup"><span data-stu-id="2f183-312">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="2f183-313">Você só precisa especificar tipos para comportamentos personalizados.</span><span class="sxs-lookup"><span data-stu-id="2f183-313">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="2f183-314">Tipos não especificados para `loadBootResource` são carregados pela estrutura de acordo com seus comportamentos de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="2f183-314">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="2f183-315">Alterar a extensão do nome de arquivo de arquivos DLL</span><span class="sxs-lookup"><span data-stu-id="2f183-315">Change the filename extension of DLL files</span></span>

<span data-ttu-id="2f183-316">Caso você tenha a necessidade de alterar as extensões de nome de arquivo dos arquivos publicados do aplicativo `.dll` , siga as orientações nesta seção.</span><span class="sxs-lookup"><span data-stu-id="2f183-316">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="2f183-317">Depois de publicar o aplicativo, use um script de shell ou o pipeline de compilação DevOps para renomear `.dll` arquivos para usar uma extensão de arquivo diferente.</span><span class="sxs-lookup"><span data-stu-id="2f183-317">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="2f183-318">Direcione os `.dll` arquivos no `wwwroot` diretório da saída publicada do aplicativo (por exemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="2f183-318">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="2f183-319">Nos exemplos a seguir, `.dll` os arquivos são renomeados para usar a `.bin` extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="2f183-319">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="2f183-320">No Windows:</span><span class="sxs-lookup"><span data-stu-id="2f183-320">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="2f183-321">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2f183-321">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="2f183-322">No Linux ou macOS:</span><span class="sxs-lookup"><span data-stu-id="2f183-322">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="2f183-323">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2f183-323">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="2f183-324">Para usar uma extensão de arquivo diferente da `.bin` , substitua `.bin` os comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="2f183-324">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="2f183-325">Para resolver os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2f183-325">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="2f183-326">Remova os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="2f183-326">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="2f183-327">A compactação está desabilitada com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="2f183-327">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="2f183-328">Recompacte o `blazor.boot.json` arquivo atualizado.</span><span class="sxs-lookup"><span data-stu-id="2f183-328">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="2f183-329">As diretrizes anteriores também se aplicam quando os ativos de trabalho do serviço estão em uso.</span><span class="sxs-lookup"><span data-stu-id="2f183-329">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="2f183-330">Remova ou recompacte `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="2f183-330">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="2f183-331">Caso contrário, as verificações de integridade de arquivo falharão no navegador.</span><span class="sxs-lookup"><span data-stu-id="2f183-331">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="2f183-332">O exemplo de Windows a seguir usa um script do PowerShell colocado na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="2f183-332">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="2f183-333">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="2f183-333">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="2f183-334">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2f183-334">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="2f183-335">No arquivo de projeto, o script é executado após a publicação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2f183-335">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="2f183-336">Para fornecer comentários, visite [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="2f183-336">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
