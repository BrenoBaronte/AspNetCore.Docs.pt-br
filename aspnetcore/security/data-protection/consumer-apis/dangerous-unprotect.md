---
title: Desproteja as cargas cujas chaves foram revogadas no ASP.NET Core
author: rick-anderson
description: Saiba como desproteger dados, protegidos com chaves que foram revogadas desde então, em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051090"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="c2e38-103">Desproteja as cargas cujas chaves foram revogadas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2e38-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="c2e38-104">O ASP.NET Core APIs de proteção de dados não se destina principalmente à persistência indefinida de cargas confidenciais.</span><span class="sxs-lookup"><span data-stu-id="c2e38-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="c2e38-105">Outras tecnologias como o [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) e o [Azure Rights Management](/rights-management/) são mais adequadas para o cenário de armazenamento indefinido e têm recursos de gerenciamento de chaves de alta segurança.</span><span class="sxs-lookup"><span data-stu-id="c2e38-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="c2e38-106">Dito isso, não há nada proibindo um desenvolvedor de usar o ASP.NET Core APIs de proteção de dados para proteção de longo prazo de dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="c2e38-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="c2e38-107">As chaves nunca são removidas do anel de chave, portanto, `IDataProtector.Unprotect` sempre é possível recuperar cargas existentes, desde que as chaves estejam disponíveis e sejam válidas.</span><span class="sxs-lookup"><span data-stu-id="c2e38-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="c2e38-108">No entanto, surge um problema quando o desenvolvedor tenta desproteger os dados que foram protegidos com uma chave revogada, como `IDataProtector.Unprotect` gerará uma exceção nesse caso.</span><span class="sxs-lookup"><span data-stu-id="c2e38-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="c2e38-109">Isso pode ser adequado para cargas de curta duração ou transitórias (como tokens de autenticação), pois esses tipos de cargas podem ser facilmente recriados pelo sistema e, no pior, o visitante do site pode precisar fazer logon novamente.</span><span class="sxs-lookup"><span data-stu-id="c2e38-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="c2e38-110">Mas, para as cargas persistentes, ter `Unprotect` throw pode levar à perda inaceitável de dados.</span><span class="sxs-lookup"><span data-stu-id="c2e38-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="c2e38-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="c2e38-111">IPersistedDataProtector</span></span>

<span data-ttu-id="c2e38-112">Para dar suporte ao cenário de permitir que as cargas sejam desprotegidas mesmo diante de chaves revogadas, o sistema de proteção de dados contém um `IPersistedDataProtector` tipo.</span><span class="sxs-lookup"><span data-stu-id="c2e38-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="c2e38-113">Para obter uma instância do `IPersistedDataProtector` , basta obter uma instância do da `IDataProtector` maneira normal e tentar converter o `IDataProtector` para `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="c2e38-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="c2e38-114">Nem todas as `IDataProtector` instâncias podem ser convertidas em `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="c2e38-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="c2e38-115">Os desenvolvedores devem usar o operador C# as ou semelhantes para evitar exceções de tempo de execução causadas por conversões inválidas e devem estar preparados para lidar adequadamente com o caso de falha.</span><span class="sxs-lookup"><span data-stu-id="c2e38-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="c2e38-116">`IPersistedDataProtector` expõe a seguinte superfície de API:</span><span class="sxs-lookup"><span data-stu-id="c2e38-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="c2e38-117">Essa API usa a carga protegida (como uma matriz de bytes) e retorna a carga não protegida.</span><span class="sxs-lookup"><span data-stu-id="c2e38-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="c2e38-118">Não há sobrecarga baseada em cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="c2e38-118">There's no string-based overload.</span></span> <span data-ttu-id="c2e38-119">Os dois parâmetros de saída são os seguintes.</span><span class="sxs-lookup"><span data-stu-id="c2e38-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="c2e38-120">`requiresMigration`: será definido como true se a chave usada para proteger essa carga não for mais a chave padrão ativa, por exemplo, a chave usada para proteger essa carga é antiga e uma operação de interrupção de chave ocorreu desde que ocorreu.</span><span class="sxs-lookup"><span data-stu-id="c2e38-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="c2e38-121">O chamador pode desejar considerar a nova proteção da carga, dependendo de suas necessidades comerciais.</span><span class="sxs-lookup"><span data-stu-id="c2e38-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="c2e38-122">`wasRevoked`: será definido como true se a chave usada para proteger essa carga tiver sido revogada.</span><span class="sxs-lookup"><span data-stu-id="c2e38-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="c2e38-123">Tenha muito cuidado ao passar `ignoreRevocationErrors: true` para o `DangerousUnprotect` método.</span><span class="sxs-lookup"><span data-stu-id="c2e38-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="c2e38-124">Se, depois de chamar esse método `wasRevoked` , o valor for true, a chave usada para proteger esse conteúdo foi revogada e a autenticidade da carga deverá ser tratada como suspeita.</span><span class="sxs-lookup"><span data-stu-id="c2e38-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="c2e38-125">Nesse caso, só continue a operar na carga desprotegida se você tiver alguma garantia separada de que ela é autêntica, por exemplo, que ela é proveniente de um banco de dados seguro em vez de ser enviada por um cliente Web não confiável.</span><span class="sxs-lookup"><span data-stu-id="c2e38-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
