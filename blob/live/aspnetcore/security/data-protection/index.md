---
title: "Proteção de Dados no ASP.NET Core"
author: rick-anderson
description: "Este documento serve como um sumário para os diversos tópicos sobre proteção de dados do ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/index
ms.openlocfilehash: 7bbd203a67b32032ba2ab82448a5fc9a495b52aa
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="data-protection-in-aspnet-core-consumer-apis-configuration-extensibility-apis-and-implementation"></a><span data-ttu-id="fe1c6-103">Proteção de Dados no ASP.NET Core: APIs de consumidor, configuração, APIs de extensibilidade e implementação</span><span class="sxs-lookup"><span data-stu-id="fe1c6-103">Data Protection in ASP.NET Core: Consumer APIs, configuration, extensibility APIs and implementation</span></span>

* [<span data-ttu-id="fe1c6-104">Introdução à proteção de dados</span><span class="sxs-lookup"><span data-stu-id="fe1c6-104">Introduction to data protection</span></span>](introduction.md)

* [<span data-ttu-id="fe1c6-105">Introdução às APIs de proteção de dados</span><span class="sxs-lookup"><span data-stu-id="fe1c6-105">Get started with the Data Protection APIs</span></span>](using-data-protection.md)

* [<span data-ttu-id="fe1c6-106">APIs de consumidor</span><span class="sxs-lookup"><span data-stu-id="fe1c6-106">Consumer APIs</span></span>](consumer-apis/index.md)

  * [<span data-ttu-id="fe1c6-107">Visão geral das APIs de consumidor</span><span class="sxs-lookup"><span data-stu-id="fe1c6-107">Consumer APIs overview</span></span>](consumer-apis/overview.md)

  * [<span data-ttu-id="fe1c6-108">Cadeias de caracteres de finalidade</span><span class="sxs-lookup"><span data-stu-id="fe1c6-108">Purpose strings</span></span>](consumer-apis/purpose-strings.md)

  * [<span data-ttu-id="fe1c6-109">Multilocação e hierarquia de finalidade</span><span class="sxs-lookup"><span data-stu-id="fe1c6-109">Purpose hierarchy and multi-tenancy</span></span>](consumer-apis/purpose-strings-multitenancy.md)

  * [<span data-ttu-id="fe1c6-110">Hash de senha</span><span class="sxs-lookup"><span data-stu-id="fe1c6-110">Password hashing</span></span>](consumer-apis/password-hashing.md)

  * [<span data-ttu-id="fe1c6-111">Limitando o tempo de vida de cargas protegidas</span><span class="sxs-lookup"><span data-stu-id="fe1c6-111">Limiting the lifetime of protected payloads</span></span>](consumer-apis/limited-lifetime-payloads.md)

  * [<span data-ttu-id="fe1c6-112">Desprotegendo cargas cujas chaves foram revogadas</span><span class="sxs-lookup"><span data-stu-id="fe1c6-112">Unprotecting payloads whose keys have been revoked</span></span>](consumer-apis/dangerous-unprotect.md)

* [<span data-ttu-id="fe1c6-113">Configuração</span><span class="sxs-lookup"><span data-stu-id="fe1c6-113">Configuration</span></span>](configuration/index.md)

  * [<span data-ttu-id="fe1c6-114">Configurar a proteção de dados</span><span class="sxs-lookup"><span data-stu-id="fe1c6-114">Configuring data protection</span></span>](configuration/overview.md)

  * [<span data-ttu-id="fe1c6-115">Configurações padrão</span><span class="sxs-lookup"><span data-stu-id="fe1c6-115">Default settings</span></span>](configuration/default-settings.md)

  * [<span data-ttu-id="fe1c6-116">Política ampla de computador</span><span class="sxs-lookup"><span data-stu-id="fe1c6-116">Machine-wide policy</span></span>](configuration/machine-wide-policy.md)

  * [<span data-ttu-id="fe1c6-117">Cenários sem reconhecimento de DI</span><span class="sxs-lookup"><span data-stu-id="fe1c6-117">Non DI-aware scenarios</span></span>](configuration/non-di-scenarios.md)

* [<span data-ttu-id="fe1c6-118">APIs de extensibilidade</span><span class="sxs-lookup"><span data-stu-id="fe1c6-118">Extensibility APIs</span></span>](extensibility/index.md)

  * [<span data-ttu-id="fe1c6-119">Extensibilidade da criptografia básica</span><span class="sxs-lookup"><span data-stu-id="fe1c6-119">Core cryptography extensibility</span></span>](extensibility/core-crypto.md)

  * [<span data-ttu-id="fe1c6-120">Extensibilidade de gerenciamento de chaves</span><span class="sxs-lookup"><span data-stu-id="fe1c6-120">Key management extensibility</span></span>](extensibility/key-management.md)

  * [<span data-ttu-id="fe1c6-121">APIs diversas</span><span class="sxs-lookup"><span data-stu-id="fe1c6-121">Miscellaneous APIs</span></span>](extensibility/misc-apis.md)

* [<span data-ttu-id="fe1c6-122">Implementação</span><span class="sxs-lookup"><span data-stu-id="fe1c6-122">Implementation</span></span>](implementation/index.md)

  * [<span data-ttu-id="fe1c6-123">Detalhes de criptografia autenticada</span><span class="sxs-lookup"><span data-stu-id="fe1c6-123">Authenticated encryption details</span></span>](implementation/authenticated-encryption-details.md)

  * [<span data-ttu-id="fe1c6-124">Derivação de subchaves e criptografia autenticada</span><span class="sxs-lookup"><span data-stu-id="fe1c6-124">Subkey derivation and authenticated encryption</span></span>](implementation/subkeyderivation.md)

  * [<span data-ttu-id="fe1c6-125">Cabeçalhos de contexto</span><span class="sxs-lookup"><span data-stu-id="fe1c6-125">Context headers</span></span>](implementation/context-headers.md)

  * [<span data-ttu-id="fe1c6-126">Gerenciamento de chaves</span><span class="sxs-lookup"><span data-stu-id="fe1c6-126">Key management</span></span>](implementation/key-management.md)

  * [<span data-ttu-id="fe1c6-127">Provedores de armazenamento de chaves</span><span class="sxs-lookup"><span data-stu-id="fe1c6-127">Key storage providers</span></span>](implementation/key-storage-providers.md)

  * [<span data-ttu-id="fe1c6-128">Criptografia de chave em repouso</span><span class="sxs-lookup"><span data-stu-id="fe1c6-128">Key encryption at rest</span></span>](implementation/key-encryption-at-rest.md)

  * [<span data-ttu-id="fe1c6-129">Imutabilidade de chave e alteração de configurações</span><span class="sxs-lookup"><span data-stu-id="fe1c6-129">Key immutability and changing settings</span></span>](implementation/key-immutability.md)

  * [<span data-ttu-id="fe1c6-130">Formato do armazenamento de chaves</span><span class="sxs-lookup"><span data-stu-id="fe1c6-130">Key storage format</span></span>](implementation/key-storage-format.md)

  * [<span data-ttu-id="fe1c6-131">Provedores de proteção de dados efêmeros</span><span class="sxs-lookup"><span data-stu-id="fe1c6-131">Ephemeral data protection providers</span></span>](implementation/key-storage-ephemeral.md)

* [<span data-ttu-id="fe1c6-132">Compatibilidade</span><span class="sxs-lookup"><span data-stu-id="fe1c6-132">Compatibility</span></span>](compatibility/index.md)

  * [<span data-ttu-id="fe1c6-133">Compartilhar cookies entre aplicativos</span><span class="sxs-lookup"><span data-stu-id="fe1c6-133">Sharing cookies between apps</span></span>](compatibility/cookie-sharing.md)

  * [<span data-ttu-id="fe1c6-134">Substituindo <machineKey> no ASP.NET</span><span class="sxs-lookup"><span data-stu-id="fe1c6-134">Replacing <machineKey> in ASP.NET</span></span>](compatibility/replacing-machinekey.md)
