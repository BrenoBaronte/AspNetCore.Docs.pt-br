---
title: Cadeias de caracteres de finalidade no ASP.NET Core
author: rick-anderson
description: Saiba como as cadeias de caracteres de finalidade são usadas no ASP.NET Core APIs de proteção de dados.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 33139f6e5e36aed2cb54738fbd2487969844edc7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018397"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="80963-103">Cadeias de caracteres de finalidade no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80963-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="80963-104">Os componentes que consomem `IDataProtectionProvider` devem passar um parâmetro de *finalidades* exclusivas para o `CreateProtector` método.</span><span class="sxs-lookup"><span data-stu-id="80963-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="80963-105">O *parâmetro* de finalidades é inerente à segurança do sistema de proteção de dados, pois ele fornece isolamento entre os consumidores criptográficos, mesmo que as chaves de criptografia raiz sejam as mesmas.</span><span class="sxs-lookup"><span data-stu-id="80963-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="80963-106">Quando um consumidor especifica uma finalidade, a cadeia de caracteres de finalidade é usada junto com as chaves de criptografia raiz para derivar subchaves de criptografia exclusivas para esse consumidor.</span><span class="sxs-lookup"><span data-stu-id="80963-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="80963-107">Isso isola o consumidor de todos os outros consumidores criptográficos no aplicativo: nenhum outro componente pode ler suas cargas e não pode ler as cargas de outro componente.</span><span class="sxs-lookup"><span data-stu-id="80963-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="80963-108">Esse isolamento também renderiza as categorias inteiras de ataques inviáveis no componente.</span><span class="sxs-lookup"><span data-stu-id="80963-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Exemplo de diagrama de finalidade](purpose-strings/_static/purposes.png)

<span data-ttu-id="80963-110">No diagrama acima, `IDataProtector` as instâncias A e B **não podem** ler as cargas de cada outra, apenas suas próprias.</span><span class="sxs-lookup"><span data-stu-id="80963-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="80963-111">A cadeia de caracteres de finalidade não precisa ser secreta.</span><span class="sxs-lookup"><span data-stu-id="80963-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="80963-112">Ele deve simplesmente ser exclusivo no sentido de que nenhum outro componente bem comparecedo fornecerá a mesma cadeia de caracteres de finalidade.</span><span class="sxs-lookup"><span data-stu-id="80963-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="80963-113">Usar o namespace e o nome do tipo do componente que consome as APIs de proteção de dados é uma boa regra geral, como na prática, essas informações nunca entrarão em conflito.</span><span class="sxs-lookup"><span data-stu-id="80963-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="80963-114">Um componente criado pela contoso, que é responsável por criar tokens de portador, pode usar contoso. Security. BearerToken como sua cadeia de caracteres de finalidade.</span><span class="sxs-lookup"><span data-stu-id="80963-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="80963-115">Ou-ainda melhor-ele pode usar contoso. Security. BearerToken. v1 como sua cadeia de caracteres de finalidade.</span><span class="sxs-lookup"><span data-stu-id="80963-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="80963-116">Acrescentar o número de versão permite que uma versão futura use contoso. Security. BearerToken. v2 como sua finalidade, e as diferentes versões seriam completamente isoladas umas das outras no que se referem a cargas.</span><span class="sxs-lookup"><span data-stu-id="80963-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="80963-117">Uma vez que o parâmetro de finalidades `CreateProtector` é uma matriz de cadeia de caracteres, a acima poderia ter sido especificada como `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="80963-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="80963-118">Isso permite estabelecer uma hierarquia de finalidades e abre a possibilidade de cenários de multilocação com o sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="80963-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="80963-119">Os componentes não devem permitir que a entrada do usuário não confiável seja a única fonte de entrada para a cadeia de finalidades.</span><span class="sxs-lookup"><span data-stu-id="80963-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="80963-120">Por exemplo, considere um componente contoso. Messaging. SecureMessage, que é responsável pelo armazenamento de mensagens seguras.</span><span class="sxs-lookup"><span data-stu-id="80963-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="80963-121">Se o componente de mensagens seguras fosse chamado `CreateProtector([ username ])` , um usuário mal-intencionado pode criar uma conta com o nome de usuário "contoso. Security. BearerToken" em uma tentativa de obter o componente a ser chamado `CreateProtector([ "Contoso.Security.BearerToken" ])` , fazendo com que o sistema de mensagens seguro possa fazer com que os tokens da autenticação possam ser percebidos como símbolos de autenticações.</span><span class="sxs-lookup"><span data-stu-id="80963-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="80963-122">Uma cadeia de finalidades para o componente de mensagens seria melhor `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , o que fornece isolamento adequado.</span><span class="sxs-lookup"><span data-stu-id="80963-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="80963-123">O isolamento fornecido pelo e os comportamentos de `IDataProtectionProvider` , `IDataProtector` e são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="80963-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="80963-124">Para um determinado `IDataProtectionProvider` objeto, o `CreateProtector` método criará um `IDataProtector` objeto vinculado exclusivamente ao `IDataProtectionProvider` objeto que o criou e o parâmetro final que foi passado para o método.</span><span class="sxs-lookup"><span data-stu-id="80963-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="80963-125">O parâmetro de finalidade não deve ser nulo.</span><span class="sxs-lookup"><span data-stu-id="80963-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="80963-126">(Se a finalidade for especificada como uma matriz, isso significa que a matriz não deve ter comprimento zero e todos os elementos da matriz devem ser não nulos.) Uma finalidade de cadeia de caracteres vazia é tecnicamente permitida, mas não é recomendada.</span><span class="sxs-lookup"><span data-stu-id="80963-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="80963-127">Dois argumentos de finalidades são equivalentes se e somente se contiverem as mesmas cadeias de caracteres (usando um comparador ordinal) na mesma ordem.</span><span class="sxs-lookup"><span data-stu-id="80963-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="80963-128">Um argumento de finalidade única é equivalente à matriz de finalidades de elemento único correspondente.</span><span class="sxs-lookup"><span data-stu-id="80963-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="80963-129">Dois `IDataProtector` objetos são equivalentes se e somente se eles forem criados a partir de objetos equivalentes `IDataProtectionProvider` com parâmetros de finalidades equivalentes.</span><span class="sxs-lookup"><span data-stu-id="80963-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="80963-130">Para um determinado `IDataProtector` objeto, uma chamada para retornará `Unprotect(protectedData)` o If original `unprotectedData` se e somente se `protectedData := Protect(unprotectedData)` for um `IDataProtector` objeto equivalente.</span><span class="sxs-lookup"><span data-stu-id="80963-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="80963-131">Não estamos considerando o caso em que algum componente escolhe intencionalmente uma cadeia de caracteres de finalidade que é conhecida como conflito com outro componente.</span><span class="sxs-lookup"><span data-stu-id="80963-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="80963-132">Esse componente, essencialmente, seria considerado mal-intencionado, e esse sistema não se destina a fornecer garantias de segurança caso o código mal-intencionado já esteja em execução dentro do processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="80963-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
