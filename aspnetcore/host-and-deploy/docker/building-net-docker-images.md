---
title: Imagens do Docker para o ASP.NET Core
author: rick-anderson
description: Saiba como usar as imagens do Docker ASP.NET Core publicadas do registro do Docker. Efetuar pull e criar suas próprias imagens.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 2cd21722082af88e536bc1001b606ee96e7cf59b
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972048"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="2738a-104">Imagens do Docker para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2738a-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="2738a-105">Este tutorial mostra como executar um aplicativo ASP.NET Core em contêineres do Docker.</span><span class="sxs-lookup"><span data-stu-id="2738a-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="2738a-106">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="2738a-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="2738a-107">Saiba mais sobre ASP.NET Core imagens do Docker</span><span class="sxs-lookup"><span data-stu-id="2738a-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="2738a-108">Baixar um aplicativo de exemplo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2738a-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="2738a-109">Executar o aplicativo de exemplo localmente</span><span class="sxs-lookup"><span data-stu-id="2738a-109">Run the sample app locally</span></span>
> * <span data-ttu-id="2738a-110">Executou o aplicativo de exemplo em contêineres do Linux</span><span class="sxs-lookup"><span data-stu-id="2738a-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="2738a-111">Executar o aplicativo de exemplo em contêineres do Windows</span><span class="sxs-lookup"><span data-stu-id="2738a-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="2738a-112">Criar e implantar manualmente</span><span class="sxs-lookup"><span data-stu-id="2738a-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="2738a-113">Imagens do ASP.NET Core Docker</span><span class="sxs-lookup"><span data-stu-id="2738a-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="2738a-114">Para este tutorial, você deve baixar um aplicativo de exemplo ASP.NET Core e executá-lo em contêineres do Docker.</span><span class="sxs-lookup"><span data-stu-id="2738a-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="2738a-115">O exemplo funciona com contêineres do Linux e do Windows.</span><span class="sxs-lookup"><span data-stu-id="2738a-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="2738a-116">O exemplo de Dockerfile usa o [recurso de build de vários estágios do Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) para compilar e executar em contêineres diferentes.</span><span class="sxs-lookup"><span data-stu-id="2738a-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="2738a-117">Os contêineres de build e execução são criados a partir de imagens que são fornecidas pela Microsoft no Hub do Docker:</span><span class="sxs-lookup"><span data-stu-id="2738a-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="2738a-118">O exemplo usa essa imagem para compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="2738a-119">A imagem contém o SDK do .NET, que inclui as ferramentas de linha de comando (CLI).</span><span class="sxs-lookup"><span data-stu-id="2738a-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="2738a-120">A imagem é otimizada para desenvolvimento local, depuração e teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="2738a-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="2738a-121">As ferramentas instaladas para desenvolvimento e compilação tornam a imagem relativamente grande.</span><span class="sxs-lookup"><span data-stu-id="2738a-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="2738a-122">O exemplo usa essa imagem para compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="2738a-123">A imagem contém o SDK do .NET Core, que inclui as ferramentas de linha de comando (CLI).</span><span class="sxs-lookup"><span data-stu-id="2738a-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="2738a-124">A imagem é otimizada para desenvolvimento local, depuração e teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="2738a-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="2738a-125">As ferramentas instaladas para desenvolvimento e compilação tornam a imagem relativamente grande.</span><span class="sxs-lookup"><span data-stu-id="2738a-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="2738a-126">O exemplo usa essa imagem para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="2738a-127">A imagem contém o runtime do ASP.NET Core e as bibliotecas e é otimizada para executar aplicativos em produção.</span><span class="sxs-lookup"><span data-stu-id="2738a-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="2738a-128">Desenvolvida para acelerar a implantação e a inicialização do aplicativo, a imagem é relativamente pequena, portanto, o desempenho da rede no registro do Docker para o host do Docker é otimizado.</span><span class="sxs-lookup"><span data-stu-id="2738a-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="2738a-129">Somente os binários e o conteúdo necessários para executar o aplicativo são copiados para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="2738a-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="2738a-130">O conteúdo está pronto para ser executado, permitindo mais rapidez do `docker run` para a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="2738a-131">A compilação de código dinâmico não é necessária no modelo do Docker.</span><span class="sxs-lookup"><span data-stu-id="2738a-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2738a-132">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="2738a-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="2738a-133">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="2738a-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="2738a-134">SDK do .NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="2738a-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="2738a-135">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="2738a-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="2738a-136">Cliente do Docker 18.03 ou posterior</span><span class="sxs-lookup"><span data-stu-id="2738a-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="2738a-137">Distribuições Linux</span><span class="sxs-lookup"><span data-stu-id="2738a-137">Linux distributions</span></span>
    * [<span data-ttu-id="2738a-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="2738a-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="2738a-139">Debian</span><span class="sxs-lookup"><span data-stu-id="2738a-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="2738a-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="2738a-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="2738a-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="2738a-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="2738a-142">macOS</span><span class="sxs-lookup"><span data-stu-id="2738a-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="2738a-143">Windows</span><span class="sxs-lookup"><span data-stu-id="2738a-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="2738a-144">Git</span><span class="sxs-lookup"><span data-stu-id="2738a-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="2738a-145">Baixar o aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="2738a-145">Download the sample app</span></span>

* <span data-ttu-id="2738a-146">Baixe o exemplo clonando o [repositório do Docker do .net](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="2738a-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="2738a-147">Executar o aplicativo localmente</span><span class="sxs-lookup"><span data-stu-id="2738a-147">Run the app locally</span></span>

* <span data-ttu-id="2738a-148">Navegue até a pasta do projeto em *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="2738a-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="2738a-149">Execute o seguinte comando para compilar e executar o aplicativo localmente:</span><span class="sxs-lookup"><span data-stu-id="2738a-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="2738a-150">Vá para `http://localhost:5000` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="2738a-151">Pressione Ctrl+C no prompt de comando para interromper o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="2738a-152">Executar em um contêiner do Linux</span><span class="sxs-lookup"><span data-stu-id="2738a-152">Run in a Linux container</span></span>

* <span data-ttu-id="2738a-153">No cliente do Docker, [alterne para contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="2738a-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="2738a-154">Navegue até a pasta do Dockerfile em *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="2738a-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="2738a-155">Execute os seguintes comandos para compilar e executar a amostra no Docker:</span><span class="sxs-lookup"><span data-stu-id="2738a-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="2738a-156">Os argumentos de comando `build`:</span><span class="sxs-lookup"><span data-stu-id="2738a-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="2738a-157">Dê o nome aspnetapp para a imagem.</span><span class="sxs-lookup"><span data-stu-id="2738a-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="2738a-158">Procure o Dockerfile na pasta atual (o ponto no final).</span><span class="sxs-lookup"><span data-stu-id="2738a-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="2738a-159">Os argumentos de comando de execução:</span><span class="sxs-lookup"><span data-stu-id="2738a-159">The run command arguments:</span></span>
  * <span data-ttu-id="2738a-160">Aloque um pseudo-TTY e mantenha-o aberto, mesmo se não estiver anexado.</span><span class="sxs-lookup"><span data-stu-id="2738a-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="2738a-161">(Mesmo efeito de `--interactive --tty`).</span><span class="sxs-lookup"><span data-stu-id="2738a-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="2738a-162">Remova automaticamente o contêiner quando ele é encerrado.</span><span class="sxs-lookup"><span data-stu-id="2738a-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="2738a-163">Mapeie a porta 5000 no computador local para a porta 80 no contêiner.</span><span class="sxs-lookup"><span data-stu-id="2738a-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="2738a-164">Dê o nome aspnetcore_sample ao contêiner.</span><span class="sxs-lookup"><span data-stu-id="2738a-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="2738a-165">Especifique a imagem aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="2738a-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="2738a-166">Vá para `http://localhost:5000` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="2738a-167">Executar em um contêiner do Windows</span><span class="sxs-lookup"><span data-stu-id="2738a-167">Run in a Windows container</span></span>

* <span data-ttu-id="2738a-168">No cliente do Docker, [alterne para contêineres do Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="2738a-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="2738a-169">Navegue até a pasta do arquivo do docker em `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="2738a-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="2738a-170">Execute os seguintes comandos para compilar e executar a amostra no Docker:</span><span class="sxs-lookup"><span data-stu-id="2738a-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="2738a-171">Para os contêineres do Windows, você precisará do endereço IP do contêiner (navegar até `http://localhost:5000` não funcionará):</span><span class="sxs-lookup"><span data-stu-id="2738a-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="2738a-172">Abra outro prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="2738a-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="2738a-173">Execute `docker ps` para ver os contêineres em execução.</span><span class="sxs-lookup"><span data-stu-id="2738a-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="2738a-174">Verifique se o contêiner "aspnetcore_sample" está lá.</span><span class="sxs-lookup"><span data-stu-id="2738a-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="2738a-175">Execute `docker exec aspnetcore_sample ipconfig` para exibir o endereço IP do contêiner.</span><span class="sxs-lookup"><span data-stu-id="2738a-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="2738a-176">A saída do comando é semelhante a este exemplo:</span><span class="sxs-lookup"><span data-stu-id="2738a-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="2738a-177">Copie o endereço IPv4 (por exemplo, 172.29.245.43) do contêiner e cole na barra de endereços do navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="2738a-178">Criar e implantar manualmente</span><span class="sxs-lookup"><span data-stu-id="2738a-178">Build and deploy manually</span></span>

<span data-ttu-id="2738a-179">Em alguns cenários, talvez você queira implantar um aplicativo em um contêiner copiando seus ativos que são necessários no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="2738a-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="2738a-180">Esta seção mostra como realizar a implantação manual.</span><span class="sxs-lookup"><span data-stu-id="2738a-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="2738a-181">Navegue até a pasta do projeto em *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="2738a-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="2738a-182">Execute o comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="2738a-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="2738a-183">Os argumentos do comando:</span><span class="sxs-lookup"><span data-stu-id="2738a-183">The command arguments:</span></span>
  * <span data-ttu-id="2738a-184">Compile o aplicativo no modo de liberação (o padrão é o modo de depuração).</span><span class="sxs-lookup"><span data-stu-id="2738a-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="2738a-185">Crie os ativos na pasta *publicada* .</span><span class="sxs-lookup"><span data-stu-id="2738a-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="2738a-186">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2738a-186">Run the app.</span></span>

  * <span data-ttu-id="2738a-187">Windows:</span><span class="sxs-lookup"><span data-stu-id="2738a-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="2738a-188">Linux:</span><span class="sxs-lookup"><span data-stu-id="2738a-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="2738a-189">Navegue até `http://localhost:5000` para ver a página inicial.</span><span class="sxs-lookup"><span data-stu-id="2738a-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="2738a-190">Para usar o aplicativo publicado manualmente dentro de um contêiner do Docker, crie um novo *Dockerfile* e use o `docker build .` comando para criar o contêiner.</span><span class="sxs-lookup"><span data-stu-id="2738a-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="2738a-191">O Dockerfile</span><span class="sxs-lookup"><span data-stu-id="2738a-191">The Dockerfile</span></span>

<span data-ttu-id="2738a-192">Aqui está o *Dockerfile* usado pelo `docker build` comando que você executou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2738a-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="2738a-193">Ele usa `dotnet publish` da mesma maneira que foi feito nesta seção para realizar a criação e implantação.</span><span class="sxs-lookup"><span data-stu-id="2738a-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="2738a-194">No *Dockerfile* anterior, os `*.csproj` arquivos são copiados e restaurados como *camadas* distintas.</span><span class="sxs-lookup"><span data-stu-id="2738a-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="2738a-195">Quando o `docker build` comando cria uma imagem, ele usa um cache interno.</span><span class="sxs-lookup"><span data-stu-id="2738a-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="2738a-196">Se os `*.csproj` arquivos não foram alterados desde a `docker build` última execução do comando, o `dotnet restore` comando não precisará ser executado novamente.</span><span class="sxs-lookup"><span data-stu-id="2738a-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="2738a-197">Em vez disso, o cache interno para a `dotnet restore` camada correspondente é reutilizado.</span><span class="sxs-lookup"><span data-stu-id="2738a-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="2738a-198">Para obter mais informações, consulte [práticas recomendadas para escrever Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="2738a-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="2738a-199">O Dockerfile</span><span class="sxs-lookup"><span data-stu-id="2738a-199">The Dockerfile</span></span>

<span data-ttu-id="2738a-200">Aqui está o *Dockerfile* usado pelo `docker build` comando que você executou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2738a-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="2738a-201">Ele usa `dotnet publish` da mesma maneira que foi feito nesta seção para realizar a criação e implantação.</span><span class="sxs-lookup"><span data-stu-id="2738a-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="2738a-202">Conforme observado no Dockerfile anterior, os `*.csproj` arquivos são copiados e restaurados como *camadas* distintas.</span><span class="sxs-lookup"><span data-stu-id="2738a-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="2738a-203">Quando o `docker build` comando cria uma imagem, ele usa um cache interno.</span><span class="sxs-lookup"><span data-stu-id="2738a-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="2738a-204">Se os `*.csproj` arquivos não foram alterados desde a `docker build` última execução do comando, o `dotnet restore` comando não precisará ser executado novamente.</span><span class="sxs-lookup"><span data-stu-id="2738a-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="2738a-205">Em vez disso, o cache interno para a `dotnet restore` camada correspondente é reutilizado.</span><span class="sxs-lookup"><span data-stu-id="2738a-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="2738a-206">Para obter mais informações, consulte [práticas recomendadas para escrever Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="2738a-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="2738a-207">O Dockerfile</span><span class="sxs-lookup"><span data-stu-id="2738a-207">The Dockerfile</span></span>

<span data-ttu-id="2738a-208">Aqui está o *Dockerfile* usado pelo `docker build` comando que você executou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2738a-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="2738a-209">Ele usa `dotnet publish` da mesma maneira que foi feito nesta seção para realizar a criação e implantação.</span><span class="sxs-lookup"><span data-stu-id="2738a-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2738a-210">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2738a-210">Additional resources</span></span>

* [<span data-ttu-id="2738a-211">Comando de build do Docker</span><span class="sxs-lookup"><span data-stu-id="2738a-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="2738a-212">Comando de execução do Docker</span><span class="sxs-lookup"><span data-stu-id="2738a-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="2738a-213">[Exemplo do Docker do ASP.NET Core](https://github.com/dotnet/dotnet-docker) (aquele usado neste tutorial).</span><span class="sxs-lookup"><span data-stu-id="2738a-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="2738a-214">Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga</span><span class="sxs-lookup"><span data-stu-id="2738a-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="2738a-215">Trabalhar com ferramentas de Docker do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2738a-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="2738a-216">Depuração com o Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2738a-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="2738a-217">GC usando o Docker e contêineres pequenos</span><span class="sxs-lookup"><span data-stu-id="2738a-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="2738a-218">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2738a-218">Next steps</span></span>

<span data-ttu-id="2738a-219">O repositório Git que contém o aplicativo de exemplo também inclui a documentação.</span><span class="sxs-lookup"><span data-stu-id="2738a-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="2738a-220">Para obter uma visão geral dos recursos disponíveis no repositório, confira [o arquivo Leiame](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="2738a-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="2738a-221">Em particular, saiba como implementar o HTTPS:</span><span class="sxs-lookup"><span data-stu-id="2738a-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2738a-222">Como desenvolver aplicativos ASP.NET Core com o Docker em HTTPS</span><span class="sxs-lookup"><span data-stu-id="2738a-222">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
