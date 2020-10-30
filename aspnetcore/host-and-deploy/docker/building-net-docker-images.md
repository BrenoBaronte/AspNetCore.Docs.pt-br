---
title: Imagens do Docker para o ASP.NET Core
author: rick-anderson
description: Saiba como usar as imagens publicadas do Docker do .NET Core no Registro do Docker. Efetuar pull de imagens e compilar suas próprias imagens.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
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
ms.openlocfilehash: 81daa1d4a996519f44e513b4f61c27cdf2b6ef5e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059800"
---
# <a name="docker-images-for-aspnet-core"></a>Imagens do Docker para o ASP.NET Core

Este tutorial mostra como executar um aplicativo ASP.NET Core em contêineres do Docker.

Neste tutorial, você:
> [!div class="checklist"]
> * Saiba mais sobre as imagens do Docker no Microsoft .NET Core
> * Baixar um aplicativo de exemplo do ASP.NET Core
> * Executar o aplicativo de exemplo localmente
> * Executou o aplicativo de exemplo em contêineres do Linux
> * Executar o aplicativo de exemplo em contêineres do Windows
> * Criar e implantar manualmente

## <a name="aspnet-core-docker-images"></a>Imagens do ASP.NET Core Docker

Para este tutorial, você deve baixar um aplicativo de exemplo ASP.NET Core e executá-lo em contêineres do Docker. O exemplo funciona com contêineres do Linux e do Windows.

O exemplo de Dockerfile usa o [recurso de build de vários estágios do Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) para compilar e executar em contêineres diferentes. Os contêineres de build e execução são criados a partir de imagens que são fornecidas pela Microsoft no Hub do Docker:

* `dotnet/core/sdk`

  O exemplo usa essa imagem para compilar o aplicativo. A imagem contém o SDK do .NET Core, que inclui as ferramentas de linha de comando (CLI). A imagem é otimizada para desenvolvimento local, depuração e teste de unidade. As ferramentas instaladas para compilação e desenvolvimento a tornam uma imagem relativamente grande. 

* `dotnet/core/aspnet`

   O exemplo usa essa imagem para executar o aplicativo. A imagem contém o runtime do ASP.NET Core e as bibliotecas e é otimizada para executar aplicativos em produção. Desenvolvida para acelerar a implantação e a inicialização do aplicativo, a imagem é relativamente pequena, portanto, o desempenho da rede no registro do Docker para o host do Docker é otimizado. Somente os binários e o conteúdo necessários para executar o aplicativo são copiados para o contêiner. O conteúdo está pronto para ser executado, permitindo mais rapidez do `Docker run` para a inicialização do aplicativo. A compilação de código dinâmico não é necessária no modelo do Docker.

## <a name="prerequisites"></a>Pré-requisitos
::: moniker range="< aspnetcore-3.0"

* [SDK do .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [SDK do .NET Core 3.0](https://dotnet.microsoft.com/download)

::: moniker-end

* Cliente do Docker 18.03 ou posterior

  * Distribuições Linux
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Baixar o aplicativo de exemplo

* Baixe o exemplo por meio da clonagem do [repositório do Docker .NET Core](https://github.com/dotnet/dotnet-docker): 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Executar o aplicativo localmente

* Navegue até a pasta do projeto em *dotnet-docker/samples/aspnetapp/aspnetapp* .

* Execute o seguinte comando para compilar e executar o aplicativo localmente:

  ```dotnetcli
  dotnet run
  ```

* Vá para `http://localhost:5000` em um navegador para testar o aplicativo.

* Pressione Ctrl+C no prompt de comando para interromper o aplicativo.

## <a name="run-in-a-linux-container"></a>Executar em um contêiner do Linux

* No cliente do Docker, [alterne para contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* Navegue até a pasta do Dockerfile em *dotnet-docker/samples/aspnetapp* .

* Execute os seguintes comandos para compilar e executar a amostra no Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  Os argumentos de comando `build`:
  * Dê o nome aspnetapp para a imagem.
  * Procure o Dockerfile na pasta atual (o ponto no final).

  Os argumentos de comando de execução:
  * Aloque um pseudo-TTY e mantenha-o aberto, mesmo se não estiver anexado. (Mesmo efeito de `--interactive --tty`).
  * Remova automaticamente o contêiner quando ele é encerrado.
  * Mapeie a porta 5000 no computador local para a porta 80 no contêiner.
  * Dê o nome aspnetcore_sample ao contêiner.
  * Especifique a imagem aspnetapp.

* Vá para `http://localhost:5000` em um navegador para testar o aplicativo.

## <a name="run-in-a-windows-container"></a>Executar em um contêiner do Windows

* No cliente do Docker, [alterne para contêineres do Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

Navegue até a pasta do arquivo do docker em `dotnet-docker/samples/aspnetapp`.

* Execute os seguintes comandos para compilar e executar a amostra no Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Para os contêineres do Windows, você precisará do endereço IP do contêiner (navegar até `http://localhost:5000` não funcionará):
  * Abra outro prompt de comando.
  * Execute `docker ps` para ver os contêineres em execução. Verifique se o contêiner "aspnetcore_sample" está lá.
  * Execute `docker exec aspnetcore_sample ipconfig` para exibir o endereço IP do contêiner. A saída do comando é semelhante a este exemplo:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Copie o endereço IPv4 (por exemplo, 172.29.245.43) do contêiner e cole na barra de endereços do navegador para testar o aplicativo.

## <a name="build-and-deploy-manually"></a>Criar e implantar manualmente

Em alguns cenários, talvez você queira implantar um aplicativo em um contêiner copiando nele os arquivos do aplicativo que são necessários no tempo de execução. Esta seção mostra como realizar a implantação manual.

* Navegue até a pasta do projeto em *dotnet-docker/samples/aspnetapp/aspnetapp* .

* Execute o comando [dotnet publish](/dotnet/core/tools/dotnet-publish):

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Os argumentos do comando:
  * Compile o aplicativo no modo de versão (o padrão é o modo de depuração).
  * Crie os arquivos na pasta *published* .

* Executar o aplicativo.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Navegue até `http://localhost:5000` para ver a página inicial.

Para usar o aplicativo publicado manualmente em um contêiner do Docker, crie um Dockerfile e use o comando `docker build .` para criar o contêiner.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>O Dockerfile

Aqui está o *Dockerfile* usado pelo `docker build` comando que você executou anteriormente.  Ele usa `dotnet publish` da mesma maneira que foi feito nesta seção para realizar a criação e implantação.  

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

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>O Dockerfile

Aqui está o *Dockerfile* usado pelo `docker build` comando que você executou anteriormente.  Ele usa `dotnet publish` da mesma maneira que foi feito nesta seção para realizar a criação e implantação.  

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

Conforme observado no Dockerfile anterior, os `*.csproj` arquivos são copiados e restaurados como *camadas* distintas. Quando o `docker build` comando cria uma imagem, ele usa um cache interno. Se os `*.csproj` arquivos não foram alterados desde a `docker build` última execução do comando, o `dotnet restore` comando não precisará ser executado novamente. Em vez disso, o cache interno para a `dotnet restore` camada correspondente é reutilizado. Para obter mais informações, consulte [práticas recomendadas para escrever Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Comando de build do Docker](https://docs.docker.com/engine/reference/commandline/build)
* [Comando de execução do Docker](https://docs.docker.com/engine/reference/commandline/run)
* [Exemplo do Docker do ASP.NET Core](https://github.com/dotnet/dotnet-docker) (aquele usado neste tutorial).
* [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](../proxy-load-balancer.md)
* [Trabalhar com ferramentas de Docker do Visual Studio](./visual-studio-tools-for-docker.md)
* [Depuração com o Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC usando o Docker e contêineres pequenos](xref:performance/memory#sc)

## <a name="next-steps"></a>Próximas etapas

O repositório Git que contém o aplicativo de exemplo também inclui a documentação. Para obter uma visão geral dos recursos disponíveis no repositório, confira [o arquivo Leiame](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md). Em particular, saiba como implementar o HTTPS:

> [!div class="nextstepaction"]
> [Como desenvolver aplicativos ASP.NET Core com o Docker em HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
