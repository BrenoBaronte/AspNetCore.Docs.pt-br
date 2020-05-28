---
Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="grpc-client-factory-integration-in-net-core"></a>integração do gRPC Client Factory no .NET Core

a integração do gRPC com `HttpClientFactory` o oferece uma maneira centralizada de criar clientes do gRPC. Ele pode ser usado como uma alternativa para [configurar instâncias de cliente gRPC](xref:grpc/client)autônomas. A integração de fábrica está disponível no pacote NuGet do [.net. ClientFactory do Grpc](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .

A fábrica oferece os seguintes benefícios:

* Fornece um local central para configurar instâncias de cliente gRPC lógicas
* Gerencia o tempo de vida do subjacente`HttpClientMessageHandler`
* Propagação automática de prazo e cancelamento em um serviço de ASP.NET Core gRPC

## <a name="register-grpc-clients"></a>Registrar clientes gRPC

Para registrar um cliente gRPC, o `AddGrpcClient` método de extensão genérico pode ser usado no `Startup.ConfigureServices` , especificando a classe de cliente tipada gRPC e o endereço de serviço:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

O tipo de cliente gRPC é registrado como transitório com injeção de dependência (DI). Agora, o cliente pode ser injetado e consumido diretamente em tipos criados por DI. ASP.NET Core controladores MVC, SignalR hubs e serviços gRPCs são locais onde os clientes gRPC podem ser injetados automaticamente:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Configurar o HttpClient

`HttpClientFactory`cria o `HttpClient` usado pelo cliente gRPC. Os `HttpClientFactory` métodos padrão podem ser usados para adicionar middleware de solicitação de saída ou para configurar o subjacente `HttpClientHandler` do `HttpClient` :

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Para obter mais informações, consulte [fazer solicitações HTTP usando IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Configurar canal e interceptores

os métodos específicos do gRPC estão disponíveis para:

* Configure um canal subjacente do cliente gRPC.
* Adicione `Interceptor` instâncias que o cliente usará ao fazer chamadas gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Data limite e propagação de cancelamento

Os clientes gRPC criados pela fábrica em um serviço gRPC podem ser configurados com `EnableCallContextPropagation()` o para propagar automaticamente o token de prazo e cancelamento para chamadas filhas. O `EnableCallContextPropagation()` método de extensão está disponível no pacote NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .

A propagação de contexto de chamada funciona lendo o data limite e o token de cancelamento do contexto de solicitação gRPC atual e propagando-os automaticamente para chamadas de saída feitas pelo cliente gRPC. A propagação de contexto de chamada é uma maneira excelente de garantir que cenários gRPC complexos e aninhados sempre propaguem o prazo e o cancelamento.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Por padrão, `EnableCallContextPropagation` o gerará um erro se o cliente for usado fora do contexto de uma chamada gRPC. O erro foi projetado para alertá-lo de que não há um contexto de chamada a ser propagado. Se você quiser usar o cliente fora de um contexto de chamada, suprimir o erro quando o cliente for configurado com `SuppressContextNotFoundErrors` :

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Para obter mais informações sobre prazos e cancelamento de RPC, consulte [ciclo de vida RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
