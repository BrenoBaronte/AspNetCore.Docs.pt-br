---
title: Serviços e clientes gRPC de primeiro código com .NET
author: jamesnk
description: Aprenda os conceitos básicos ao escrever o código gRPC primeiro com o .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880628"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="3c7f5-103">Serviços e clientes gRPC de primeiro código com .NET</span><span class="sxs-lookup"><span data-stu-id="3c7f5-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="3c7f5-104">Por [James Newton – King](https://twitter.com/jamesnk) e [Marc Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="3c7f5-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="3c7f5-105">O gRPC de código usa os tipos .NET para definir contratos de serviço e de mensagem.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="3c7f5-106">O Code-First é uma boa opção quando um sistema inteiro usa o .NET:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="3c7f5-107">Os tipos de contrato de dados e de serviço .NET podem ser compartilhados entre o servidor .NET e os clientes.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="3c7f5-108">Evita a necessidade de definir contratos em `.proto` arquivos e geração de código.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="3c7f5-109">O Code-First não é recomendado em sistemas poliglota com vários idiomas.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="3c7f5-110">Os tipos de contrato de dados e serviço .NET não podem ser usados com plataformas non-.NET.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="3c7f5-111">Para chamar um serviço gRPC escrito usando Code-First, outras plataformas devem criar um `.proto` contrato que corresponda ao serviço.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="3c7f5-112">protobuf-net. Grpc</span><span class="sxs-lookup"><span data-stu-id="3c7f5-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3c7f5-113">Para obter ajuda com protobuf-net. Grpc, visite o [protobuf-net. Grpc site da Web](https://protobuf-net.github.io/protobuf-net.Grpc/) ou crie um problema no [protobuf-net. Repositório GitHub Grpc](https://github.com/protobuf-net/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="3c7f5-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="3c7f5-114">[protobuf-net. Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) é um projeto da Comunidade e não tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="3c7f5-115">Ele adiciona o suporte de código primeiro a `Grpc.AspNetCore` e `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="3c7f5-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="3c7f5-116">Ele usa tipos do .NET anotados com atributos para definir os serviços e as mensagens do gRPC de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="3c7f5-117">A primeira etapa para criar um serviço de gRPC de código primeiro é definir o contrato de código:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="3c7f5-118">Crie um novo projeto que será compartilhado pelo servidor e pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="3c7f5-119">Adicione um [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Referência de pacote Grpc.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="3c7f5-120">Criar tipos de contrato de serviço e de dados.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="3c7f5-121">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-121">The preceding code:</span></span>

* <span data-ttu-id="3c7f5-122">Define `HelloRequest` e `HelloReply` mensagens.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="3c7f5-123">Define a `IGreeterService` interface do contrato com o `SayHelloAsync` método gRPC unário.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="3c7f5-124">O contrato de serviço é implementado no servidor e chamado do cliente.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="3c7f5-125">Os métodos definidos nas interfaces de serviço devem corresponder a determinadas assinaturas, dependendo se são unários, streaming de servidor, streaming de cliente ou streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="3c7f5-126">Para obter mais informações sobre como definir contratos de serviço, consulte [protobuf-net. Documentação de introdução do Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span><span class="sxs-lookup"><span data-stu-id="3c7f5-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="3c7f5-127">Criar um serviço gRPC de primeiro código</span><span class="sxs-lookup"><span data-stu-id="3c7f5-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="3c7f5-128">Para adicionar o gRPC Code-First a um aplicativo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="3c7f5-129">Adicione um [protobuf-net. Referência de pacote Grpc. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="3c7f5-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="3c7f5-130">Adicione uma referência ao projeto de contrato de código compartilhado.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="3c7f5-131">Crie um novo `GreeterService.cs` arquivo e implemente a `IGreeterService` interface do serviço:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="3c7f5-132">Atualize o `Startup.cs` arquivo:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="3c7f5-133">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-133">In the preceding code:</span></span>

* <span data-ttu-id="3c7f5-134">`AddCodeFirstGrpc` registra os serviços que habilitam o código primeiro.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="3c7f5-135">`MapGrpcService<GreeterService>` Adiciona o ponto de extremidade de serviço do primeiro código.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="3c7f5-136">os serviços gRPCs implementados com Code-First e `.proto` arquivos podem coexistir no mesmo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="3c7f5-137">Todos os serviços gRPCs usam a [configuração do serviço gRPC](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="3c7f5-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="3c7f5-138">Criar um cliente gRPC do primeiro código</span><span class="sxs-lookup"><span data-stu-id="3c7f5-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="3c7f5-139">Um cliente gRPC de primeiro código usa o contrato de serviço para chamar os serviços do gRPC.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="3c7f5-140">Para chamar um serviço gRPC usando um cliente do primeiro código:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="3c7f5-141">Adicione um [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Referência de pacote Grpc.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="3c7f5-142">Adicione uma referência ao projeto de contrato de código compartilhado.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="3c7f5-143">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="3c7f5-143">The preceding code:</span></span>

* <span data-ttu-id="3c7f5-144">Cria um canal gRPC.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="3c7f5-145">Cria um cliente de primeiro código do canal com o `CreateGrpcService<IGreeterService>` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="3c7f5-146">Chama o serviço gRPC com `SayHelloAsync` .</span><span class="sxs-lookup"><span data-stu-id="3c7f5-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="3c7f5-147">Um cliente gRPC de código primeiro é criado a partir de um canal.</span><span class="sxs-lookup"><span data-stu-id="3c7f5-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="3c7f5-148">Assim como um cliente regular, um cliente de primeiro código usa sua [configuração de canal](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="3c7f5-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c7f5-149">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3c7f5-149">Additional resources</span></span>

* [<span data-ttu-id="3c7f5-150">protobuf-net. Site do Grpc</span><span class="sxs-lookup"><span data-stu-id="3c7f5-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="3c7f5-151">protobuf-net. Repositório GitHub do Grpc</span><span class="sxs-lookup"><span data-stu-id="3c7f5-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
