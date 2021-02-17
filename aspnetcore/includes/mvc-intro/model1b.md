---
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
ms.openlocfilehash: 3c21d2a5604c2dfc96624fb5d161537797925fef
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551703"
---
<span data-ttu-id="b942d-101">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="b942d-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="b942d-102">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="b942d-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="b942d-103">O campo `Id` que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="b942d-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="b942d-104">`[DataType(DataType.Date)]`: O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="b942d-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="b942d-105">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="b942d-105">With this attribute:</span></span>

  * <span data-ttu-id="b942d-106">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="b942d-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="b942d-107">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="b942d-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="b942d-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="b942d-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>