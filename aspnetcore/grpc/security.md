---
title: Considerações de segurança no gRPC para ASP.NET Core
author: jamesnk
description: Saiba mais sobre as considerações de segurança para o gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: 45ac0916a368cf68f4d40e14298a7628446989ee
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252806"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="03d4e-103">Considerações de segurança no gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03d4e-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="03d4e-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="03d4e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="03d4e-105">Este artigo fornece informações sobre como proteger o gRPC com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="03d4e-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="03d4e-106">Segurança de transporte</span><span class="sxs-lookup"><span data-stu-id="03d4e-106">Transport security</span></span>

<span data-ttu-id="03d4e-107">as mensagens gRPC são enviadas e recebidas usando HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="03d4e-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="03d4e-108">Recomendamos:</span><span class="sxs-lookup"><span data-stu-id="03d4e-108">We recommend:</span></span>

* <span data-ttu-id="03d4e-109">O protocolo [TLS](https://tools.ietf.org/html/rfc5246) é usado para proteger mensagens em aplicativos gRPC de produção.</span><span class="sxs-lookup"><span data-stu-id="03d4e-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="03d4e-110">os serviços gRPCs devem escutar e responder apenas por portas seguras.</span><span class="sxs-lookup"><span data-stu-id="03d4e-110">gRPC services should only listen and respond over secured ports.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="03d4e-111">O TLS está configurado em Kestrel.</span><span class="sxs-lookup"><span data-stu-id="03d4e-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="03d4e-112">Para obter mais informações sobre como configurar pontos de extremidade do Kestrel, consulte [configuração do ponto de extremidades do Kestrel](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="03d4e-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>
::: moniker-end

::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="03d4e-113">O TLS está configurado em Kestrel.</span><span class="sxs-lookup"><span data-stu-id="03d4e-113">TLS is configured in Kestrel.</span></span> <span data-ttu-id="03d4e-114">Para obter mais informações sobre como configurar pontos de extremidade do Kestrel, consulte [configuração do ponto de extremidades do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="03d4e-114">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
::: moniker-end

## <a name="exceptions"></a><span data-ttu-id="03d4e-115">Exceções</span><span class="sxs-lookup"><span data-stu-id="03d4e-115">Exceptions</span></span>

<span data-ttu-id="03d4e-116">As mensagens de exceção geralmente são consideradas dados confidenciais que não devem ser revelados a um cliente.</span><span class="sxs-lookup"><span data-stu-id="03d4e-116">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="03d4e-117">Por padrão, o gRPC não envia os detalhes de uma exceção gerada por um serviço gRPC para o cliente.</span><span class="sxs-lookup"><span data-stu-id="03d4e-117">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="03d4e-118">Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro.</span><span class="sxs-lookup"><span data-stu-id="03d4e-118">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="03d4e-119">A entrega de mensagem de exceção ao cliente pode ser substituída (por exemplo, em desenvolvimento ou teste) com [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="03d4e-119">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="03d4e-120">As mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.</span><span class="sxs-lookup"><span data-stu-id="03d4e-120">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="03d4e-121">Limites de tamanho de mensagem</span><span class="sxs-lookup"><span data-stu-id="03d4e-121">Message size limits</span></span>

<span data-ttu-id="03d4e-122">As mensagens de entrada para clientes e serviços do gRPC são carregadas na memória.</span><span class="sxs-lookup"><span data-stu-id="03d4e-122">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="03d4e-123">Os limites de tamanho de mensagem são um mecanismo para ajudar a impedir que o gRPC consuma recursos excessivos.</span><span class="sxs-lookup"><span data-stu-id="03d4e-123">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="03d4e-124">o gRPC usa limites de tamanho por mensagem para gerenciar mensagens de entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="03d4e-124">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="03d4e-125">Por padrão, o gRPC limita as mensagens de entrada para 4 MB.</span><span class="sxs-lookup"><span data-stu-id="03d4e-125">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="03d4e-126">Não há limite para mensagens de saída.</span><span class="sxs-lookup"><span data-stu-id="03d4e-126">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="03d4e-127">No servidor, os limites de mensagens gRPC podem ser configurados para todos os serviços em um aplicativo com `AddGrpc` :</span><span class="sxs-lookup"><span data-stu-id="03d4e-127">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="03d4e-128">Os limites também podem ser configurados para um serviço individual usando o `AddServiceOptions<TService>` .</span><span class="sxs-lookup"><span data-stu-id="03d4e-128">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="03d4e-129">Para obter mais informações sobre como configurar limites de tamanho de mensagem, consulte [configuração do gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="03d4e-129">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="03d4e-130">Validação de certificado do cliente</span><span class="sxs-lookup"><span data-stu-id="03d4e-130">Client certificate validation</span></span>

<span data-ttu-id="03d4e-131">Os [certificados de cliente](https://tools.ietf.org/html/rfc5246#section-7.4.4) são inicialmente validados quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="03d4e-131">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="03d4e-132">Por padrão, o Kestrel não executa validação adicional do certificado de cliente de uma conexão.</span><span class="sxs-lookup"><span data-stu-id="03d4e-132">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="03d4e-133">Recomendamos que os serviços gRPCs protegidos por certificados de cliente usem o pacote [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) .</span><span class="sxs-lookup"><span data-stu-id="03d4e-133">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="03d4e-134">ASP.NET Core autenticação de certificação executará validação adicional em um certificado de cliente, incluindo:</span><span class="sxs-lookup"><span data-stu-id="03d4e-134">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="03d4e-135">O certificado tem um EKU (uso estendido de chave) válido</span><span class="sxs-lookup"><span data-stu-id="03d4e-135">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="03d4e-136">Está dentro do período de validade</span><span class="sxs-lookup"><span data-stu-id="03d4e-136">Is within its validity period</span></span>
* <span data-ttu-id="03d4e-137">Verificar revogação de certificado</span><span class="sxs-lookup"><span data-stu-id="03d4e-137">Check certificate revocation</span></span>
