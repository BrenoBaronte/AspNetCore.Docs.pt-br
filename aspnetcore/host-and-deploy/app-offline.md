---
title: Arquivo offline do aplicativo (app_offline.htm)
author: rick-anderson
description: Saiba como o arquivo offline do aplicativo ( `app_offline.htm` ) funciona com o módulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94431024"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="cf0e5-103">Arquivo offline do aplicativo ( `app_offline.htm` )</span><span class="sxs-lookup"><span data-stu-id="cf0e5-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="cf0e5-104">O arquivo offline do aplicativo ( `app_offline.htm` ) é usado pelo módulo ASP.NET Core para desligar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="cf0e5-105">Se um arquivo com o nome `app_offline.htm` for detectado no diretório raiz de um aplicativo, o módulo ASP.NET Core tentará desligar o aplicativo normalmente e interromperá o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="cf0e5-106">Se o aplicativo ainda estiver em execução após o número de segundos definido em `shutdownTimeLimit` , o módulo ASP.NET Core interromperá o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="cf0e5-107">Enquanto o `app_offline.htm` arquivo estiver presente, o módulo ASP.NET Core responde às solicitações enviando de volta o conteúdo do `app_offline.htm` arquivo.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="cf0e5-108">Quando o `app_offline.htm` arquivo é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="cf0e5-109">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cf0e5-110">Por exemplo, uma conexão WebSocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="cf0e5-111">Arquivos de implantação bloqueados</span><span class="sxs-lookup"><span data-stu-id="cf0e5-111">Locked deployment files</span></span>

<span data-ttu-id="cf0e5-112">Os arquivos na pasta de implantação são bloqueados quando o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="cf0e5-113">Os arquivos bloqueados não podem ser substituídos durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="cf0e5-114">`app_offline.htm` é o mecanismo principal para liberar arquivos bloqueados.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="cf0e5-115">`app_offline.htm` é usado pelo Implantação da Web para parar e iniciar o aplicativo corretamente.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="cf0e5-116">`app_offline.htm` pode ser usado manualmente para iniciar e parar o aplicativo (requer o PowerShell 5 ou posterior):</span><span class="sxs-lookup"><span data-stu-id="cf0e5-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

<span data-ttu-id="cf0e5-117">No script anterior do PowerShell:</span><span class="sxs-lookup"><span data-stu-id="cf0e5-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="cf0e5-118">O espaço reservado `{PATH TO APP}` é o caminho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="cf0e5-119">O `New-Item` comando para o pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="cf0e5-120">O `Remove-Item` comando inicia o pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="cf0e5-121">Os comandos entre o `New-Item` comando e o `Remove-Item` comando são fornecidos pelo desenvolvedor para implantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="cf0e5-122">Os arquivos também podem ser desbloqueados interrompendo manualmente o pool de aplicativos no Gerenciador do IIS no servidor.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="cf0e5-123">Não use o `app_offine.htm` arquivo ao usar o Gerenciador do IIS para parar e reiniciar o pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="cf0e5-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
