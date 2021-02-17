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
ms.openlocfilehash: c96c5e66d2e15db03c321d239a64b98119a7543a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551932"
---
<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="7a5cb-101">Adicionar regras de validação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="7a5cb-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="7a5cb-102">O namespace DataAnnotations fornece um conjunto de atributos de validação internos aplicados de forma declarativa a uma classe ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-102">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="7a5cb-103">DataAnnotations também contém atributos de formatação como `DataType`, que ajudam com a formatação e não fornecem validação.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-103">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="7a5cb-104">Atualize a classe `Movie` para aproveitar os atributos de validação `Required`, `StringLength`, `RegularExpression` e `Range` internos.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-104">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="7a5cb-105">Os atributos de validação especificam o comportamento que você deseja impor nas propriedades de modelo às quais eles são aplicados:</span><span class="sxs-lookup"><span data-stu-id="7a5cb-105">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="7a5cb-106">Os atributos `Required` e `MinimumLength` indicam que uma propriedade deve ter um valor; porém, nada impede que um usuário insira um espaço em branco para atender a essa validação.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-106">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="7a5cb-107">O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-107">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="7a5cb-108">No código anterior, "Gênero":</span><span class="sxs-lookup"><span data-stu-id="7a5cb-108">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="7a5cb-109">Deve usar apenas letras.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-109">Must only use letters.</span></span>
  * <span data-ttu-id="7a5cb-110">A primeira letra deve ser maiúscula.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-110">The first letter is required to be uppercase.</span></span> <span data-ttu-id="7a5cb-111">Espaços em branco, números e caracteres especiais não são permitidos.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-111">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="7a5cb-112">A "Classificação" `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="7a5cb-112">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="7a5cb-113">Exige que o primeiro caractere seja uma letra maiúscula.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-113">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="7a5cb-114">Permite caracteres especiais e números nos espaços subsequentes.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-114">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="7a5cb-115">"PG-13" é válido para uma classificação, mas é um erro em "Gênero".</span><span class="sxs-lookup"><span data-stu-id="7a5cb-115">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="7a5cb-116">O atributo `Range` restringe um valor a um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-116">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="7a5cb-117">O atributo `StringLength` permite definir o tamanho máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu tamanho mínimo.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-117">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="7a5cb-118">Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-118">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="7a5cb-119">Ter as regras de validação automaticamente impostas pelo ASP.NET Core ajuda a tornar seu aplicativo mais robusto.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-119">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="7a5cb-120">Também garante que você não se esqueça de validar algo e inadvertidamente permita dados incorretos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7a5cb-120">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>
