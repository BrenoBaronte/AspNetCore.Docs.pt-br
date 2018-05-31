---
title: Usar o modelo de projeto do React com Redux com o ASP.NET Core
author: SteveSandersonMS
description: Saiba como começar a trabalhar com o modelo de projeto do SPA (Aplicativo de Página Única) do ASP.NET Core para React com Redux e create-react-app.
manager: wpickett
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
ms.devlang: csharp
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: spa/react-with-redux
ms.openlocfilehash: 7ec4f6d53a4723ace087b1dc256de7845cb44cc6
ms.sourcegitcommit: 466300d32f8c33e64ee1b419a2cbffe702863cdf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2018
ms.locfileid: "34555216"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="b6eca-103">Usar o modelo de projeto do React com Redux com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6eca-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> <span data-ttu-id="b6eca-104">Esta documentação não é sobre o modelo de projeto do React com Redux incluído no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="b6eca-104">This documentation isn't about the React-with-Redux project template included in ASP.NET Core 2.0.</span></span> <span data-ttu-id="b6eca-105">Ela é sobre o modelo React com Redux mais recente, para o qual você pode atualizar manualmente.</span><span class="sxs-lookup"><span data-stu-id="b6eca-105">It's about the newer React-with-Redux template to which you can update manually.</span></span> <span data-ttu-id="b6eca-106">O modelo está incluído no ASP.NET Core 2.1 por padrão.</span><span class="sxs-lookup"><span data-stu-id="b6eca-106">The template is included in ASP.NET Core 2.1 by default.</span></span>

::: moniker-end

<span data-ttu-id="b6eca-107">O modelo de projeto do React com Redux atualizado fornece um ponto inicial conveniente para aplicativos do ASP.NET Core usando convenções do React, do Redux e de CRA [(create-react-app)](https://github.com/facebookincubator/create-react-app) para implementar uma IU (interface do usuário) avançada do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b6eca-107">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="b6eca-108">Com exceção do comando de criação de projeto, todas as informações sobre o modelo React com Redux são as mesmas que aquelas sobre o modelo React.</span><span class="sxs-lookup"><span data-stu-id="b6eca-108">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="b6eca-109">Para criar esse tipo de projeto, execute `dotnet new reactredux` em vez de `dotnet new react`.</span><span class="sxs-lookup"><span data-stu-id="b6eca-109">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="b6eca-110">Para obter mais informações sobre a funcionalidade comum para ambos os modelos baseados em reagir, confira a [Documentação do modelo React](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="b6eca-110">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>
