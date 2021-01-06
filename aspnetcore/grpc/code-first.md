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
# <a name="code-first-grpc-services-and-clients-with-net"></a>Serviços e clientes gRPC de primeiro código com .NET

Por [James Newton – King](https://twitter.com/jamesnk) e [Marc Gravell](https://twitter.com/marcgravell)

O gRPC de código usa os tipos .NET para definir contratos de serviço e de mensagem.

O Code-First é uma boa opção quando um sistema inteiro usa o .NET:

* Os tipos de contrato de dados e de serviço .NET podem ser compartilhados entre o servidor .NET e os clientes.
* Evita a necessidade de definir contratos em `.proto` arquivos e geração de código.

O Code-First não é recomendado em sistemas poliglota com vários idiomas. Os tipos de contrato de dados e serviço .NET não podem ser usados com plataformas non-.NET. Para chamar um serviço gRPC escrito usando Code-First, outras plataformas devem criar um `.proto` contrato que corresponda ao serviço.

## <a name="protobuf-netgrpc"></a>protobuf-net. Grpc

> [!IMPORTANT]
> Para obter ajuda com protobuf-net. Grpc, visite o [protobuf-net. Grpc site da Web](https://protobuf-net.github.io/protobuf-net.Grpc/) ou crie um problema no [protobuf-net. Repositório GitHub Grpc](https://github.com/protobuf-net/protobuf-net.Grpc).

[protobuf-net. Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) é um projeto da Comunidade e não tem suporte da Microsoft. Ele adiciona o suporte de código primeiro a `Grpc.AspNetCore` e `Grpc.Net.Client` . Ele usa tipos do .NET anotados com atributos para definir os serviços e as mensagens do gRPC de um aplicativo.

A primeira etapa para criar um serviço de gRPC de código primeiro é definir o contrato de código:

* Crie um novo projeto que será compartilhado pelo servidor e pelo cliente.
* Adicione um [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Referência de pacote Grpc.
* Criar tipos de contrato de serviço e de dados.

[!code-csharp[](code-first/Contracts.cs)]

O código anterior:

* Define `HelloRequest` e `HelloReply` mensagens.
* Define a `IGreeterService` interface do contrato com o `SayHelloAsync` método gRPC unário.

O contrato de serviço é implementado no servidor e chamado do cliente. Os métodos definidos nas interfaces de serviço devem corresponder a determinadas assinaturas, dependendo se são unários, streaming de servidor, streaming de cliente ou streaming bidirecional.

Para obter mais informações sobre como definir contratos de serviço, consulte [protobuf-net. Documentação de introdução do Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).

## <a name="create-a-code-first-grpc-service"></a>Criar um serviço gRPC de primeiro código

Para adicionar o gRPC Code-First a um aplicativo ASP.NET Core:

* Adicione um [protobuf-net. Referência de pacote Grpc. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) .
* Adicione uma referência ao projeto de contrato de código compartilhado.

Crie um novo `GreeterService.cs` arquivo e implemente a `IGreeterService` interface do serviço:

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

Atualize o `Startup.cs` arquivo:

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

No código anterior:

* `AddCodeFirstGrpc` registra os serviços que habilitam o código primeiro.
* `MapGrpcService<GreeterService>` Adiciona o ponto de extremidade de serviço do primeiro código.

os serviços gRPCs implementados com Code-First e `.proto` arquivos podem coexistir no mesmo aplicativo. Todos os serviços gRPCs usam a [configuração do serviço gRPC](xref:grpc/configuration#configure-services-options).

## <a name="create-a-code-first-grpc-client"></a>Criar um cliente gRPC do primeiro código

Um cliente gRPC de primeiro código usa o contrato de serviço para chamar os serviços do gRPC. Para chamar um serviço gRPC usando um cliente do primeiro código:

* Adicione um [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Referência de pacote Grpc.
* Adicione uma referência ao projeto de contrato de código compartilhado.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

O código anterior:

* Cria um canal gRPC.
* Cria um cliente de primeiro código do canal com o `CreateGrpcService<IGreeterService>` método de extensão.
* Chama o serviço gRPC com `SayHelloAsync` .

Um cliente gRPC de código primeiro é criado a partir de um canal. Assim como um cliente regular, um cliente de primeiro código usa sua [configuração de canal](xref:grpc/configuration#configure-client-options).

## <a name="additional-resources"></a>Recursos adicionais

* [protobuf-net. Site do Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net. Repositório GitHub do Grpc](https://github.com/protobuf-net/protobuf-net.Grpc)
