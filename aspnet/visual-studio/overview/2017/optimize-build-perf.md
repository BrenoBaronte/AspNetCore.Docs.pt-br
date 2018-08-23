---
uid: visual-studio/overview/2017/optimize-build-perf
title: Otimizar o desempenho de build de solução
author: tfitzmac
description: Otimizar o desempenho de build de solução
ms.author: riande
ms.date: 08/22/2018
msc.type: authoredcontent
ms.openlocfilehash: 19f190835e7477e69db470b74edac9e211fd9158
ms.sourcegitcommit: 5a2456cbf429069dc48aaa2823cde14100e4c438
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "41909940"
---
# <a name="optimize-build-performance-for-solution"></a><span data-ttu-id="6c407-103">Otimizar o desempenho de build de solução</span><span class="sxs-lookup"><span data-stu-id="6c407-103">Optimize build performance for solution</span></span>
<span data-ttu-id="6c407-104">15,8 de 2017 do Visual Studio e posteriormente adicionado um novo item de menu sob **Build > compilação do ASP.NET > otimizar o desempenho de compilação para a solução**.</span><span class="sxs-lookup"><span data-stu-id="6c407-104">Visual Studio 2017 15.8 and later added a new menu item under **Build > ASP.NET Compilation > Optimize Build Performance for Solution**.</span></span>

![captura de tela do novo item de menu](optimize-build-perf/_static/optimize-build-performance-for-solution.png)

<span data-ttu-id="6c407-106">O ASP.NET compila seus modos de exibição em tempo de execução, o que significa que seu projeto ASP.NET carrega uma cópia do compilador.</span><span class="sxs-lookup"><span data-stu-id="6c407-106">ASP.NET compiles its views at runtime, which means your ASP.NET project carries with it a copy of the compiler.</span></span> <span data-ttu-id="6c407-107">No entanto, em um computador de desenvolvedor quando a cópia do compilador não corresponde à cópia do Visual Studio, o desempenho de compilação é afetado na ordem de 1 a 3 segundos por compilação incremental.</span><span class="sxs-lookup"><span data-stu-id="6c407-107">However, on a developer machine when the copy of the compiler doesn't match Visual Studio's copy, your build performance is impacted on the order of 1-3 seconds per incremental build.</span></span> <span data-ttu-id="6c407-108">Esse recurso atualizará a cópia do seu projeto do compilador para corresponder ao Visual Studio qual devem acelerar as compilações incrementais.</span><span class="sxs-lookup"><span data-stu-id="6c407-108">This feature will update your project's copy of the compiler to match Visual Studio's which should speed up incremental builds.</span></span>

<span data-ttu-id="6c407-109">Isso é aplicável a somente projetos de estrutura do ASP.NET, ele não se aplica ao ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6c407-109">This is applicable to ASP.NET Framework projects only, it does not apply to ASP.NET Core.</span></span>
