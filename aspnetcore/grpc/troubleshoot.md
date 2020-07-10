---
title: Solucionar problemas do gRPC no .NET Core
author: jamesnk
description: Solucionar erros ao usar o gRPC no .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/troubleshoot
ms.openlocfilehash: 385291ec6bb6719a5fade927fa9f599af8c94045
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176174"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Solucionar problemas do gRPC no .NET Core

Por [James Newton – King](https://twitter.com/jamesnk)

Este documento aborda problemas comumente encontrados ao desenvolver aplicativos gRPC no .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Incompatibilidade entre a configuração de SSL/TLS do cliente e do serviço

O modelo gRPC e os exemplos usam o protocolo TLS para proteger os serviços [do](https://tools.ietf.org/html/rfc5246) gRPC por padrão. Os clientes gRPC precisam usar uma conexão segura para chamar os serviços gRPC protegidos com êxito.

Você pode verificar se o serviço gRPC do ASP.NET Core está usando o TLS nos logs gravados no início do aplicativo. O serviço escutará em um ponto de extremidade HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

O cliente .NET Core deve usar `https` no endereço do servidor para fazer chamadas com uma conexão segura:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Todas as implementações de cliente do gRPC dão suporte a TLS. Os clientes gRPC de outras linguagens normalmente exigem o canal configurado com o `SslCredentials` . `SslCredentials`Especifica o certificado que o cliente usará e deve ser usado em vez de credenciais não seguras. Para obter exemplos de como configurar as diferentes implementações de cliente gRPC para usar o TLS, consulte [autenticação do gRPC](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Chamar um serviço gRPC com um certificado não confiável/inválido

O cliente .NET gRPC requer que o serviço tenha um certificado confiável. A seguinte mensagem de erro é retornada ao chamar um serviço gRPC sem um certificado confiável:

> Exceção sem tratamento. Sistema .net. http. HttpRequestexception: não foi possível estabelecer a conexão SSL, consulte a exceção interna.
> ---> System.Security.Authentication.AuthenticationException: o certificado remoto é inválido de acordo com o procedimento de validação.

Você poderá ver esse erro se estiver testando seu aplicativo localmente e o ASP.NET Core certificado de desenvolvimento HTTPS não for confiável. Para obter instruções sobre como corrigir esse problema, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core no Windows e no macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Se você estiver chamando um serviço gRPC em outro computador e não puder confiar no certificado, o cliente gRPC poderá ser configurado para ignorar o certificado inválido. O código a seguir usa [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) para permitir chamadas sem um certificado confiável:

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Certificados não confiáveis só devem ser usados durante o desenvolvimento do aplicativo. Os aplicativos de produção sempre devem usar certificados válidos.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Chamar serviços gRPCs inseguros com o cliente .NET Core

A configuração adicional é necessária para chamar serviços gRPCs inseguros com o cliente .NET Core. O cliente gRPC deve definir o `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` comutador para `true` e usar `http` no endereço do servidor:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS

O Kestrel não dá suporte a HTTP/2 com TLS no macOS e versões mais antigas do Windows, como o Windows 7. O modelo ASP.NET Core gRPC e os exemplos usam TLS por padrão. Você verá a seguinte mensagem de erro ao tentar iniciar o servidor gRPC:

> Não é possível associar ao https://localhost:5001 na interface de loopback IPv4: ' http/2 sobre TLS não tem suporte no MacOS devido ao suporte de ALPN ausente. '.

Para contornar esse problema, configure o Kestrel e o cliente gRPC para usar HTTP/2 *sem* TLS. Você só deve fazer isso durante o desenvolvimento. Não usar o TLS fará com que as mensagens gRPC sejam enviadas sem criptografia.

Kestrel deve configurar um ponto de extremidade HTTP/2 sem TLS em *Program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Quando um ponto de extremidade HTTP/2 é configurado sem TLS, o [ListenerOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deve ser definido como `HttpProtocols.Http2` . `HttpProtocols.Http1AndHttp2`Não pode ser usado porque o TLS é necessário para negociar HTTP/2. Sem o TLS, todas as conexões com o ponto de extremidade padrão para HTTP/1.1 e chamadas gRPC falham.

O cliente gRPC também deve ser configurado para não usar o TLS. Para obter mais informações, consulte [chamar serviços gRPCs inseguros com o cliente .NET Core](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> O HTTP/2 sem TLS só deve ser usado durante o desenvolvimento do aplicativo. Os aplicativos de produção sempre devem usar a segurança de transporte. Para obter mais informações, consulte [Security Considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>os ativos do C# gRPC não são gerados pelo código de arquivos. proto

a geração de código gRPC de clientes concretos e classes base de serviço requer que os arquivos e as ferramentas do protobuf sejam referenciados de um projeto. Você deve incluir:

* arquivos *. proto* que você deseja usar no `<Protobuf>` grupo de itens. [Arquivos *. proto* importados](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) devem ser referenciados pelo projeto.
* Referência de pacote para o pacote de ferramentas do gRPC [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).

Para obter mais informações sobre como gerar ativos do gRPC C#, consulte <xref:grpc/basics> .

Um ASP.NET Core aplicativo Web que hospeda serviços gRPCs precisa apenas da classe base de serviço gerada:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Um aplicativo cliente gRPC que faz chamadas gRPC precisa apenas do cliente concreto gerado:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>Projetos do WPF não podem gerar ativos gRPC C# a partir de arquivos. proto

Os projetos do WPF têm um [problema conhecido](https://github.com/dotnet/wpf/issues/810) que impede a geração de código gRPC de funcionar corretamente. Quaisquer tipos de gRPC gerados em um projeto do WPF referenciando `Grpc.Tools` e *. proto* arquivos criarão erros de compilação quando usados:

> erro CS0246: não foi possível encontrar o nome do namespace ou tipo ' MyGrpcServices ' (está faltando uma diretiva using ou uma referência de assembly?)

Você pode solucionar esse problema:

1. Crie um novo projeto de biblioteca de classes do .NET Core.
2. No novo projeto, adicione referências para habilitar a [geração de código C# a partir de arquivos * \* . proto* ](xref:grpc/basics#generated-c-assets):
    * Adicione uma referência de pacote ao pacote [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .
    * Adicione arquivos * \* . proto* ao `<Protobuf>` grupo de itens.
3. No aplicativo do WPF, adicione uma referência ao novo projeto.

O aplicativo WPF pode usar os tipos gerados gRPC do novo projeto de biblioteca de classes.

[!INCLUDE[](~/includes/gRPCazure.md)]
