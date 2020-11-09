---
title: Autorização baseada em exibição no ASP.NET Core MVC
author: rick-anderson
description: 'Este documento demonstra como injetar e utilizar o serviço de autorização dentro de uma Razor exibição de ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/views
ms.openlocfilehash: b3d6e595aa08208f2bf9e95d7070cf9c24802b62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061321"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="1bf1f-103">Autorização baseada em exibição no ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1bf1f-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="1bf1f-104">Geralmente, um desenvolvedor deseja mostrar, ocultar ou modificar uma interface do usuário com base na identidade atual.</span><span class="sxs-lookup"><span data-stu-id="1bf1f-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="1bf1f-105">Você pode acessar o serviço de autorização nas exibições do MVC por meio da [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1bf1f-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1bf1f-106">Para injetar o serviço de autorização em uma Razor exibição, use a `@inject` diretiva:</span><span class="sxs-lookup"><span data-stu-id="1bf1f-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="1bf1f-107">Se você quiser o serviço de autorização em cada exibição, coloque a `@inject` diretiva no arquivo *_ViewImports. cshtml* do diretório *views* .</span><span class="sxs-lookup"><span data-stu-id="1bf1f-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="1bf1f-108">Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1bf1f-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="1bf1f-109">Use o serviço de autorização injetado para invocar `AuthorizeAsync` exatamente da mesma maneira que você verificaria durante [a autorização baseada em recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="1bf1f-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="1bf1f-110">Em alguns casos, o recurso será seu modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="1bf1f-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="1bf1f-111">Invoque `AuthorizeAsync` exatamente da mesma maneira que você verificaria durante [a autorização baseada em recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="1bf1f-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="1bf1f-112">No código anterior, o modelo é passado como um recurso que a avaliação da política deve levar em consideração.</span><span class="sxs-lookup"><span data-stu-id="1bf1f-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="1bf1f-113">Não confie na alternância de visibilidade dos elementos da interface do usuário de seu aplicativo como a única verificação de autorização.</span><span class="sxs-lookup"><span data-stu-id="1bf1f-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="1bf1f-114">Ocultar um elemento de interface do usuário pode não impedir completamente o acesso à sua ação de controlador associada.</span><span class="sxs-lookup"><span data-stu-id="1bf1f-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="1bf1f-115">Por exemplo, considere o botão no trecho de código anterior.</span><span class="sxs-lookup"><span data-stu-id="1bf1f-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="1bf1f-116">Um usuário poderá invocar o `Edit` método de ação se ele souber que a URL de recurso relativa é */Document/Edit/1* .</span><span class="sxs-lookup"><span data-stu-id="1bf1f-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1* .</span></span> <span data-ttu-id="1bf1f-117">Por esse motivo, o `Edit` método de ação deve executar sua própria verificação de autorização.</span><span class="sxs-lookup"><span data-stu-id="1bf1f-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
