---
title: Hospedar e implantar o ASP.NET Core
author: rick-anderson
description: Aprenda como configurar ambientes de hospedagem e implantar aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/index
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556613"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="b1c8c-103">Hospedar e implantar o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1c8c-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b1c8c-104">Em geral, implantar um aplicativo ASP.NET Core em um ambiente de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="b1c8c-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="b1c8c-105">Implante o aplicativo publicado em uma pasta no servidor de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="b1c8c-106">Configure um gerenciador de processo que inicia o aplicativo quando a solicitação chega e reinicia-o depois que ele falha ou que o servidor é reinicializado.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="b1c8c-107">No caso da configuração de um proxy reverso, defina um proxy reverso para encaminhar solicitações para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="b1c8c-108">Publicar em uma pasta</span><span class="sxs-lookup"><span data-stu-id="b1c8c-108">Publish to a folder</span></span>

<span data-ttu-id="b1c8c-109">O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila o código do aplicativo e copia os arquivos necessários para executar o aplicativo em uma pasta *publish*.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="b1c8c-110">Ao implantar do Visual Studio, a etapa `dotnet publish` ocorre automaticamente antes de os arquivos serem copiados para o destino da implantação.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="b1c8c-111">Publicar arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="b1c8c-111">Publish settings files</span></span>

<span data-ttu-id="b1c8c-112">`*.json` os arquivos são publicados por padrão.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-112">`*.json` files are published by default.</span></span> <span data-ttu-id="b1c8c-113">Para publicar outros arquivos de configurações, especifique-os em um [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) elemento no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="b1c8c-114">O exemplo a seguir publica arquivos XML:</span><span class="sxs-lookup"><span data-stu-id="b1c8c-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="b1c8c-115">Conteúdo da pasta</span><span class="sxs-lookup"><span data-stu-id="b1c8c-115">Folder contents</span></span>

<span data-ttu-id="b1c8c-116">A pasta *publish* contém um ou mais arquivos do assembly, dependências e, opcionalmente, o runtime do .NET.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="b1c8c-117">Um aplicativo .NET Core pode ser publicado como uma *implantação autocontida* ou uma *implantação dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="b1c8c-118">Se o aplicativo for autocontido, os arquivos do assembly que contêm o runtime do .NET serão incluídos na pasta *publish*.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="b1c8c-119">Se o aplicativo depender da estrutura, os arquivos de runtime do .NET não serão incluídos porque o aplicativo tem uma referência para uma versão do .NET que está instalada no servidor.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="b1c8c-120">O modelo de implantação padrão é dependente da estrutura.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="b1c8c-121">Para obter mais informações, consulte [implantação de aplicativos do .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="b1c8c-122">Além de arquivos *.exe* e *.dll*, a pasta *publish* para um aplicativo ASP.NET Core normalmente contém arquivos de configuração, ativos estáticos e exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="b1c8c-123">Para obter mais informações, consulte <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="b1c8c-124">Configure um gerenciador de processo</span><span class="sxs-lookup"><span data-stu-id="b1c8c-124">Set up a process manager</span></span>

<span data-ttu-id="b1c8c-125">Um aplicativo ASP.NET Core é um aplicativo de console que deve ser iniciado quando um servidor é inicializado e reiniciado após falhas.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="b1c8c-126">Para automatizar inicializações e reinicializações, um gerenciador de processo é necessário.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="b1c8c-127">Os gerenciadores de processo mais comuns para o ASP.NET Core são:</span><span class="sxs-lookup"><span data-stu-id="b1c8c-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="b1c8c-128">Linux</span><span class="sxs-lookup"><span data-stu-id="b1c8c-128">Linux</span></span>
  * [<span data-ttu-id="b1c8c-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="b1c8c-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="b1c8c-130">Apache</span><span class="sxs-lookup"><span data-stu-id="b1c8c-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="b1c8c-131">Windows</span><span class="sxs-lookup"><span data-stu-id="b1c8c-131">Windows</span></span>
  * [<span data-ttu-id="b1c8c-132">IIS</span><span class="sxs-lookup"><span data-stu-id="b1c8c-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="b1c8c-133">Serviço do Windows</span><span class="sxs-lookup"><span data-stu-id="b1c8c-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="b1c8c-134">Configurar um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="b1c8c-134">Set up a reverse proxy</span></span>

<span data-ttu-id="b1c8c-135">Se o aplicativo usar o servidor Web do servidor [Kestrel](xref:fundamentals/servers/kestrel), você poderá usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) ou [IIS](xref:host-and-deploy/iis/index) como um servidor proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="b1c8c-136">Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="b1c8c-137">Qualquer configuração&mdash;com ou sem um servidor proxy reverso&mdash;é uma configuração de hospedagem compatível.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="b1c8c-138">Para obter mais informações, consulte [Quando usar Kestrel com um proxy reverso](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b1c8c-139">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="b1c8c-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b1c8c-140">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="b1c8c-141">Sem configuração adicional, um aplicativo pode não ter acesso ao esquema (HTTP/HTTPS) e ao endereço IP remoto em que uma solicitação foi originada.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="b1c8c-142">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="b1c8c-143">Usar o Visual Studio e o MSBuild para automatizar as implantações</span><span class="sxs-lookup"><span data-stu-id="b1c8c-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="b1c8c-144">A implantação muitas vezes requer tarefas adicionais além de copiar a saída da [dotnet publish](/dotnet/core/tools/dotnet-publish) para um servidor.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="b1c8c-145">Por exemplo, arquivos extras podem ser necessários ou excluídos da pasta *publish*.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="b1c8c-146">O Visual Studio usa o [MSBuild](/visualstudio/msbuild/msbuild) para implantação na Web e o MSBuild pode ser personalizado para realizar muitas outras tarefas durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-146">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="b1c8c-147">Para saber mais, confira <xref:host-and-deploy/visual-studio-publish-profiles> e o livro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="b1c8c-148">Você pode implantar diretamente do Visual Studio para o Serviço de Aplicativo do Azure usando [o recurso Publicar na Web](xref:tutorials/publish-to-azure-webapp-using-vs) ou usando o [suporte ao Git interno](xref:host-and-deploy/azure-apps/azure-continuous-deployment).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="b1c8c-149">O Azure DevOps Services dá suporte à [implantação contínua para o Serviço de Aplicativo do Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="b1c8c-150">Para obter mais informações, confira [DevOps com ASP.NET Core e Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="b1c8c-151">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="b1c8c-151">Publish to Azure</span></span>

<span data-ttu-id="b1c8c-152">Confira <xref:tutorials/publish-to-azure-webapp-using-vs> para obter instruções sobre como publicar um aplicativo no Azure usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="b1c8c-153">Um exemplo adicional é fornecido em [Criar um aplicativo Web ASP.NET Core no Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-153">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="b1c8c-154">Publicar com MSDeploy no Windows</span><span class="sxs-lookup"><span data-stu-id="b1c8c-154">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="b1c8c-155">Confira <xref:host-and-deploy/visual-studio-publish-profiles> para obter instruções sobre como publicar um aplicativo com um perfil de publicação do Visual Studio, inclusive de um prompt de comando do Windows, usando o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-155">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="b1c8c-156">Serviços de Informações da Internet (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1c8c-156">Internet Information Services (IIS)</span></span>

<span data-ttu-id="b1c8c-157">Para implantações no Serviços de Informações da Internet (IIS) com a configuração fornecida pelo arquivo de *web.config* , consulte os artigos em <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="b1c8c-157">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="b1c8c-158">Hospedar em uma web farm</span><span class="sxs-lookup"><span data-stu-id="b1c8c-158">Host in a web farm</span></span>

<span data-ttu-id="b1c8c-159">Para obter informações sobre a configuração para hospedar aplicativos do ASP.NET Core em um ambiente de web farm (por exemplo, a implantação de várias instâncias do aplicativo para escalabilidade), veja <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-159">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="b1c8c-160">Host no Docker</span><span class="sxs-lookup"><span data-stu-id="b1c8c-160">Host on Docker</span></span>

<span data-ttu-id="b1c8c-161">Para obter mais informações, consulte <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-161">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="b1c8c-162">Executar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="b1c8c-162">Perform health checks</span></span>

<span data-ttu-id="b1c8c-163">Use o Middleware de verificação de integridade para executar verificações de integridade em um aplicativo e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-163">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="b1c8c-164">Para obter mais informações, consulte <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-164">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1c8c-165">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b1c8c-165">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="b1c8c-166">Hospedagem ASP.NET</span><span class="sxs-lookup"><span data-stu-id="b1c8c-166">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b1c8c-167">Em geral, implantar um aplicativo ASP.NET Core em um ambiente de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="b1c8c-167">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="b1c8c-168">Implante o aplicativo publicado em uma pasta no servidor de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-168">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="b1c8c-169">Configure um gerenciador de processo que inicia o aplicativo quando a solicitação chega e reinicia-o depois que ele falha ou que o servidor é reinicializado.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-169">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="b1c8c-170">No caso da configuração de um proxy reverso, defina um proxy reverso para encaminhar solicitações para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-170">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="b1c8c-171">Publicar em uma pasta</span><span class="sxs-lookup"><span data-stu-id="b1c8c-171">Publish to a folder</span></span>

<span data-ttu-id="b1c8c-172">O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila o código do aplicativo e copia os arquivos necessários para executar o aplicativo em uma pasta *publish*.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-172">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="b1c8c-173">Ao implantar do Visual Studio, a etapa `dotnet publish` ocorre automaticamente antes de os arquivos serem copiados para o destino da implantação.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-173">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="b1c8c-174">Conteúdo da pasta</span><span class="sxs-lookup"><span data-stu-id="b1c8c-174">Folder contents</span></span>

<span data-ttu-id="b1c8c-175">A pasta *publish* contém um ou mais arquivos do assembly, dependências e, opcionalmente, o runtime do .NET.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-175">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="b1c8c-176">Um aplicativo .NET Core pode ser publicado como uma *implantação autocontida* ou uma *implantação dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-176">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="b1c8c-177">Se o aplicativo for autocontido, os arquivos do assembly que contêm o runtime do .NET serão incluídos na pasta *publish*.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-177">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="b1c8c-178">Se o aplicativo depender da estrutura, os arquivos de runtime do .NET não serão incluídos porque o aplicativo tem uma referência para uma versão do .NET que está instalada no servidor.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-178">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="b1c8c-179">O modelo de implantação padrão é dependente da estrutura.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-179">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="b1c8c-180">Para obter mais informações, consulte [implantação de aplicativos do .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-180">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="b1c8c-181">Além de arquivos *.exe* e *.dll*, a pasta *publish* para um aplicativo ASP.NET Core normalmente contém arquivos de configuração, ativos estáticos e exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-181">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="b1c8c-182">Para obter mais informações, consulte <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-182">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="b1c8c-183">Configure um gerenciador de processo</span><span class="sxs-lookup"><span data-stu-id="b1c8c-183">Set up a process manager</span></span>

<span data-ttu-id="b1c8c-184">Um aplicativo ASP.NET Core é um aplicativo de console que deve ser iniciado quando um servidor é inicializado e reiniciado após falhas.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-184">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="b1c8c-185">Para automatizar inicializações e reinicializações, um gerenciador de processo é necessário.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-185">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="b1c8c-186">Os gerenciadores de processo mais comuns para o ASP.NET Core são:</span><span class="sxs-lookup"><span data-stu-id="b1c8c-186">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="b1c8c-187">Linux</span><span class="sxs-lookup"><span data-stu-id="b1c8c-187">Linux</span></span>
  * [<span data-ttu-id="b1c8c-188">Nginx</span><span class="sxs-lookup"><span data-stu-id="b1c8c-188">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="b1c8c-189">Apache</span><span class="sxs-lookup"><span data-stu-id="b1c8c-189">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="b1c8c-190">Windows</span><span class="sxs-lookup"><span data-stu-id="b1c8c-190">Windows</span></span>
  * [<span data-ttu-id="b1c8c-191">IIS</span><span class="sxs-lookup"><span data-stu-id="b1c8c-191">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="b1c8c-192">Serviço do Windows</span><span class="sxs-lookup"><span data-stu-id="b1c8c-192">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="b1c8c-193">Configurar um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="b1c8c-193">Set up a reverse proxy</span></span>

<span data-ttu-id="b1c8c-194">Se o aplicativo usar o servidor Web do servidor [Kestrel](xref:fundamentals/servers/kestrel), você poderá usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) ou [IIS](xref:host-and-deploy/iis/index) como um servidor proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-194">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="b1c8c-195">Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-195">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="b1c8c-196">Qualquer configuração&mdash;com ou sem um servidor proxy reverso&mdash;é uma configuração de hospedagem compatível.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-196">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="b1c8c-197">Para obter mais informações, consulte [Quando usar Kestrel com um proxy reverso](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-197">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b1c8c-198">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="b1c8c-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b1c8c-199">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-199">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="b1c8c-200">Sem configuração adicional, um aplicativo pode não ter acesso ao esquema (HTTP/HTTPS) e ao endereço IP remoto em que uma solicitação foi originada.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-200">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="b1c8c-201">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="b1c8c-202">Usar o Visual Studio e o MSBuild para automatizar as implantações</span><span class="sxs-lookup"><span data-stu-id="b1c8c-202">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="b1c8c-203">A implantação muitas vezes requer tarefas adicionais além de copiar a saída da [dotnet publish](/dotnet/core/tools/dotnet-publish) para um servidor.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-203">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="b1c8c-204">Por exemplo, arquivos extras podem ser necessários ou excluídos da pasta *publish*.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-204">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="b1c8c-205">O MSBuild, que é usado pelo Visual Studio para implantação da Web, pode ser personalizado para fazer muitas outras tarefas durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-205">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="b1c8c-206">Para saber mais, confira <xref:host-and-deploy/visual-studio-publish-profiles> e o livro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-206">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="b1c8c-207">Você pode implantar diretamente do Visual Studio para o Serviço de Aplicativo do Azure usando [o recurso Publicar na Web](xref:tutorials/publish-to-azure-webapp-using-vs) ou usando o [suporte ao Git interno](xref:host-and-deploy/azure-apps/azure-continuous-deployment).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-207">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="b1c8c-208">O Azure DevOps Services dá suporte à [implantação contínua para o Serviço de Aplicativo do Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-208">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="b1c8c-209">Para obter mais informações, confira [DevOps com ASP.NET Core e Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-209">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="b1c8c-210">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="b1c8c-210">Publish to Azure</span></span>

<span data-ttu-id="b1c8c-211">Confira <xref:tutorials/publish-to-azure-webapp-using-vs> para obter instruções sobre como publicar um aplicativo no Azure usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-211">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="b1c8c-212">Um exemplo adicional é fornecido em [Criar um aplicativo Web ASP.NET Core no Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-212">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="b1c8c-213">Publicar com MSDeploy no Windows</span><span class="sxs-lookup"><span data-stu-id="b1c8c-213">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="b1c8c-214">Confira <xref:host-and-deploy/visual-studio-publish-profiles> para obter instruções sobre como publicar um aplicativo com um perfil de publicação do Visual Studio, inclusive de um prompt de comando do Windows, usando o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="b1c8c-214">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="b1c8c-215">Serviços de Informações da Internet (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1c8c-215">Internet Information Services (IIS)</span></span>

<span data-ttu-id="b1c8c-216">Para implantações no Serviços de Informações da Internet (IIS) com a configuração fornecida pelo arquivo de *web.config* , consulte os artigos em <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="b1c8c-216">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="b1c8c-217">Hospedar em uma web farm</span><span class="sxs-lookup"><span data-stu-id="b1c8c-217">Host in a web farm</span></span>

<span data-ttu-id="b1c8c-218">Para obter informações sobre a configuração para hospedar aplicativos do ASP.NET Core em um ambiente de web farm (por exemplo, a implantação de várias instâncias do aplicativo para escalabilidade), veja <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-218">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="b1c8c-219">Host no Docker</span><span class="sxs-lookup"><span data-stu-id="b1c8c-219">Host on Docker</span></span>

<span data-ttu-id="b1c8c-220">Para obter mais informações, consulte <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="b1c8c-220">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1c8c-221">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b1c8c-221">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="b1c8c-222">Hospedagem ASP.NET</span><span class="sxs-lookup"><span data-stu-id="b1c8c-222">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
