---
title: Introdução ao gRPC no .NET Core
author: juntaoluo
description: Saiba mais sobre os serviços de gRPC com a pilha do ASP.NET Core e o servidor Kestrel.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/index
ms.openlocfilehash: 2d7d683051fd1eb97f3f57d75bd582109166a6cd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768842"
---
# <a name="introduction-to-grpc-on-net-core"></a>Introdução ao gRPC no .NET Core

Por [John Luo](https://github.com/juntaoluo) e [James Newton – King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) é uma estrutura de RPC (Chamada de Procedimento Remoto) de linguagem independente de alto desempenho.

Os principais benefícios de gRPC são:
* Estrutura RPC leve, de alto desempenho e moderna.
* Desenvolvimento da API de primeiro contrato, usando buffers de protocolo, por padrão, permitindo implementações independente de linguagem.
* As ferramentas disponíveis para várias linguagens gerarem clientes e servidores fortemente tipados.
* Dá suporte ao cliente, servidor e chamadas bi-direcionais de streaming.
* Uso de rede reduzida com a serialização binária Protobuf.

Esses benefícios tornam o gRPC ideal para:
* Microsserviços leves em que a eficiência é crítica.
* Sistemas poliglotas nos quais múltiplas linguagens são necessárias para o desenvolvimento.
* Serviços ponto a ponto em tempo real que precisam lidar com solicitações ou respostas de streaming.

## <a name="c-tooling-support-for-proto-files"></a>Suporte de ferramentas C# para arquivos. proto

o gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API. Serviços e mensagens são definidos em * \*arquivos. proto* :

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Os tipos .net para serviços, clientes e mensagens são gerados automaticamente com * \** a inclusão de arquivos. proto em um projeto:

* Adicione uma referência de pacote ao pacote [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .
* Adicione * \*arquivos. proto* ao grupo `<Protobuf>` de itens.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Para obter mais informações sobre o suporte de ferramentas do <xref:grpc/basics>gRPC, consulte.

## <a name="grpc-services-on-aspnet-core"></a>serviços gRPCs no ASP.NET Core

os serviços gRPCs podem ser hospedados em ASP.NET Core. Os serviços têm integração total com recursos populares de ASP.NET Core, como registro em log, injeção de dependência (DI), autenticação e autorização.

O modelo de projeto de serviço gRPC fornece um serviço inicial:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService`herda do `GreeterBase` tipo, que é gerado a `Greeter` partir do serviço no arquivo * \*. proto* . O serviço torna-se acessível para clientes no *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Para saber mais sobre os serviços gRPCs no ASP.NET Core <xref:grpc/aspnetcore>, consulte.

## <a name="call-grpc-services-with-a-net-client"></a>Chamar serviços gRPCs com um cliente .NET

Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de * \*arquivos. proto* ](xref:grpc/basics#generated-c-assets). O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no arquivo * \*. proto* .

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Um cliente gRPC é criado usando um canal, que representa uma conexão de vida longa para um serviço gRPC. Um canal pode ser criado usando `GrpcChannel.ForAddress`.

Para obter mais informações sobre a criação de clientes e a chamada de métodos <xref:grpc/client>de serviço diferentes, consulte.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
