---
title: Introdução à autorização no ASP.NET Core
author: rick-anderson
description: Aprenda as noções básicas de autorização e como funciona a autorização em aplicativos ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
uid: security/authorization/introduction
ms.openlocfilehash: 7d7570ead1365588fd582d9bea364685da29a576
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634430"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="ee22d-103">Introdução à autorização no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee22d-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="ee22d-104">A autorização refere-se ao processo que determina o que um usuário pode fazer.</span><span class="sxs-lookup"><span data-stu-id="ee22d-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="ee22d-105">Por exemplo, um usuário administrativo tem permissão para criar uma biblioteca de documentos, adicionar documentos, editar documentos e excluí-los.</span><span class="sxs-lookup"><span data-stu-id="ee22d-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="ee22d-106">Um usuário não administrativo que trabalha com a biblioteca está autorizado apenas a ler os documentos.</span><span class="sxs-lookup"><span data-stu-id="ee22d-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="ee22d-107">A autorização é ortogonal e independente da autenticação.</span><span class="sxs-lookup"><span data-stu-id="ee22d-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="ee22d-108">No entanto, a autorização requer um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="ee22d-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="ee22d-109">A autenticação é o processo de garantir quem é um usuário.</span><span class="sxs-lookup"><span data-stu-id="ee22d-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="ee22d-110">A autenticação pode criar uma ou mais identidades para o usuário atual.</span><span class="sxs-lookup"><span data-stu-id="ee22d-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="ee22d-111">Para obter mais informações sobre autenticação no ASP.NET Core, consulte <xref:security/authentication/index> .</span><span class="sxs-lookup"><span data-stu-id="ee22d-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="ee22d-112">Tipos de autorização</span><span class="sxs-lookup"><span data-stu-id="ee22d-112">Authorization types</span></span>

<span data-ttu-id="ee22d-113">ASP.NET Core autorização fornece uma [função](xref:security/authorization/roles) declarativa simples e um modelo avançado [baseado em políticas](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="ee22d-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="ee22d-114">A autorização é expressa em requisitos, e os manipuladores avaliam as declarações de um usuário em relação aos requisitos.</span><span class="sxs-lookup"><span data-stu-id="ee22d-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="ee22d-115">As verificações imperativas podem ser baseadas em políticas simples ou políticas que avaliam a identidade do usuário e as propriedades do recurso que o usuário está tentando acessar.</span><span class="sxs-lookup"><span data-stu-id="ee22d-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="ee22d-116">Namespaces</span><span class="sxs-lookup"><span data-stu-id="ee22d-116">Namespaces</span></span>

<span data-ttu-id="ee22d-117">Os componentes de autorização, incluindo os `AuthorizeAttribute` `AllowAnonymousAttribute` atributos e, são encontrados no `Microsoft.AspNetCore.Authorization` namespace.</span><span class="sxs-lookup"><span data-stu-id="ee22d-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="ee22d-118">Consulte a documentação sobre [autorização simples](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="ee22d-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
