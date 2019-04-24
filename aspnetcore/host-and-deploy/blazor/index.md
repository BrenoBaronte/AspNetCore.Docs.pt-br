---
title: Hospedar e implantar o Blazor
author: guardrex
description: Descubra como hospedar e implantar aplicativos Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 774fbb6fdaab14a015db4fb39de2e1ea73a1837b
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982634"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="10c8a-103">Hospedar e implantar o Blazor</span><span class="sxs-lookup"><span data-stu-id="10c8a-103">Host and deploy Blazor</span></span>

<span data-ttu-id="10c8a-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="10c8a-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="10c8a-105">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="10c8a-105">Publish the app</span></span>

<span data-ttu-id="10c8a-106">Os aplicativos são publicados para implantação na configuração de versão com o comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="10c8a-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="10c8a-107">Um IDE (ambiente de desenvolvimento integrado) pode manipular a execução do comando `dotnet publish` automaticamente usando recursos de publicação internos, para que não seja necessário executar o comando manualmente usando um prompt de comando, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="10c8a-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="10c8a-108">O `dotnet publish` dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação.</span><span class="sxs-lookup"><span data-stu-id="10c8a-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="10c8a-109">Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.</span><span class="sxs-lookup"><span data-stu-id="10c8a-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="10c8a-110">A implantação é criada na pasta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="10c8a-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="10c8a-111">Os ativos na pasta *publish* são implantados no servidor Web.</span><span class="sxs-lookup"><span data-stu-id="10c8a-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="10c8a-112">A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="10c8a-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="10c8a-113">Implantação</span><span class="sxs-lookup"><span data-stu-id="10c8a-113">Deployment</span></span>

<span data-ttu-id="10c8a-114">Confira orientações de implantação nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="10c8a-114">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>