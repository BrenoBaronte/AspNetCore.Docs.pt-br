---
title: Detalhes de criptografia autenticados no ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação da criptografia autenticada da proteção de dados do ASP.NET Core.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: ed75ab235a95a88bbe60615526137b4c2bb719ef
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630842"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="55416-103">Detalhes de criptografia autenticados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55416-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="55416-104">Chamadas para IDataProtector. Protect são operações de criptografia autenticadas.</span><span class="sxs-lookup"><span data-stu-id="55416-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="55416-105">O método Protect oferece confidencialidade e autenticidade e está vinculado à cadeia de finalidade que foi usada para derivar essa instância IDataProtector específica de seu IDataProtectionProvider raiz.</span><span class="sxs-lookup"><span data-stu-id="55416-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="55416-106">IDataProtector. Protect usa um parâmetro de texto não criptografado de byte [] e produz uma carga protegida de byte [], cujo formato é descrito abaixo.</span><span class="sxs-lookup"><span data-stu-id="55416-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="55416-107">(Há também uma sobrecarga de método de extensão que usa um parâmetro de cadeia de caracteres de texto não criptografado e retorna uma carga protegida por cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="55416-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="55416-108">Se essa API for usada, o formato da carga protegida ainda terá a estrutura abaixo, mas será [codificado em base64url](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="55416-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="55416-109">Formato da carga protegida</span><span class="sxs-lookup"><span data-stu-id="55416-109">Protected payload format</span></span>

<span data-ttu-id="55416-110">O formato da carga protegida consiste em três componentes principais:</span><span class="sxs-lookup"><span data-stu-id="55416-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="55416-111">Um cabeçalho mágico de 32 bits que identifica a versão do sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="55416-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="55416-112">Uma ID de chave de 128 bits que identifica a chave usada para proteger essa carga específica.</span><span class="sxs-lookup"><span data-stu-id="55416-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="55416-113">O restante da carga protegida é [específico do criptografador encapsulado por essa chave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="55416-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="55416-114">No exemplo a seguir, a chave representa um criptografador AES-256-CBC + HMACSHA256 e a carga é ainda mais subdividida da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="55416-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="55416-115">Um modificador de chave de 128 bits.</span><span class="sxs-lookup"><span data-stu-id="55416-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="55416-116">Um vetor de inicialização de 128 bits.</span><span class="sxs-lookup"><span data-stu-id="55416-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="55416-117">48 bytes de saída AES-256-CBC.</span><span class="sxs-lookup"><span data-stu-id="55416-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="55416-118">Uma marca de autenticação HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="55416-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="55416-119">Um conteúdo protegido de exemplo é ilustrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="55416-119">A sample protected payload is illustrated below.</span></span>

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

<span data-ttu-id="55416-120">Do formato de carga acima dos primeiros 32 bits, ou 4 bytes são o cabeçalho mágico que identifica a versão (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="55416-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="55416-121">Os próximos 128 bits ou 16 bytes é o identificador de chave (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF 57 EE)</span><span class="sxs-lookup"><span data-stu-id="55416-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="55416-122">O resto contém a carga e é específico do formato usado.</span><span class="sxs-lookup"><span data-stu-id="55416-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="55416-123">Todas as cargas protegidas a uma determinada chave começarão com o mesmo cabeçalho de 20 bytes (valor mágico, ID da chave).</span><span class="sxs-lookup"><span data-stu-id="55416-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="55416-124">Os administradores podem usar esse fato para fins de diagnóstico aproximados quando uma carga foi gerada.</span><span class="sxs-lookup"><span data-stu-id="55416-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="55416-125">Por exemplo, a carga acima corresponde à chave {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="55416-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="55416-126">Se, depois de verificar o repositório de chaves, você descobrir que essa data de ativação da chave específica foi 2015-01-01 e sua data de expiração foi 2015-03-01, é razoável pressupor que a carga (se não violada) foi gerada dentro dessa janela, dar ou pegar um pequeno fator adequações em ambos os lados.</span><span class="sxs-lookup"><span data-stu-id="55416-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
