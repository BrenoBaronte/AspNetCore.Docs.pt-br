---
title: ASP.NET Core teste de carga/estresse
author: Jeremy-Meng
description: Saiba mais sobre várias ferramentas e abordagens notáveis para teste de carga e teste de estresse ASP.NET Core aplicativos.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: 5df2dd906d52aaec4fc13b07f3d92c87c802f37f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406505"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="e98d7-103">ASP.NET Core teste de carga/estresse</span><span class="sxs-lookup"><span data-stu-id="e98d7-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="e98d7-104">Testes de carga e testes de estresse são importantes para garantir que um aplicativo Web seja eficaz e escalonável.</span><span class="sxs-lookup"><span data-stu-id="e98d7-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="e98d7-105">Suas metas são diferentes, embora elas geralmente compartilhem testes semelhantes.</span><span class="sxs-lookup"><span data-stu-id="e98d7-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="e98d7-106">**Testes de carga**: teste se o aplicativo pode tratar de uma carga especificada de usuários para um determinado cenário e ainda atender à meta de resposta.</span><span class="sxs-lookup"><span data-stu-id="e98d7-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="e98d7-107">O aplicativo é executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="e98d7-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="e98d7-108">**Testes de estresse**: teste a estabilidade do aplicativo ao executar sob condições extremas, geralmente por um longo período de tempo.</span><span class="sxs-lookup"><span data-stu-id="e98d7-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="e98d7-109">Os testes colocam alta carga de usuário, picos ou aumentam a carga gradualmente, no aplicativo, ou limitam os recursos de computação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e98d7-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="e98d7-110">Testes de estresse determinam se um aplicativo sob estresse pode se recuperar de uma falha e retornar normalmente para o comportamento esperado.</span><span class="sxs-lookup"><span data-stu-id="e98d7-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="e98d7-111">Sob estresse, o aplicativo não é executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="e98d7-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="e98d7-112">O Visual Studio 2019 anunciou planos para [substituir o teste de carga](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="e98d7-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="e98d7-113">O serviço de teste de carga baseado em nuvem DevOps do Azure correspondente foi fechado.</span><span class="sxs-lookup"><span data-stu-id="e98d7-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="e98d7-114">Ferramentas de terceiros</span><span class="sxs-lookup"><span data-stu-id="e98d7-114">Third-party tools</span></span>

<span data-ttu-id="e98d7-115">A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:</span><span class="sxs-lookup"><span data-stu-id="e98d7-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="e98d7-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="e98d7-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="e98d7-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="e98d7-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="e98d7-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="e98d7-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="e98d7-119">K6</span><span class="sxs-lookup"><span data-stu-id="e98d7-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="e98d7-120">Locust</span><span class="sxs-lookup"><span data-stu-id="e98d7-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="e98d7-121">Websurto de vento oeste</span><span class="sxs-lookup"><span data-stu-id="e98d7-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="e98d7-122">Netling</span><span class="sxs-lookup"><span data-stu-id="e98d7-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="e98d7-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="e98d7-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)