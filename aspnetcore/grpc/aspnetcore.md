---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 1a5510364ee46165e275d07073ab087d79d65313
ms.sourcegitcommit: 50d3e939a90c5480df480f651dda032901468dd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819036"
---
# <a name="grpc-services-with-aspnet-core"></a>Serviços do gRPC com o ASP.NET Core

Este documento mostra como começar a usar os serviços gRPCs usando ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Introdução ao serviço de gRPC no ASP.NET Core

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Consulte Introdução [aos serviços gRPCs](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Adicionar serviços gRPCs a um aplicativo ASP.NET Core

gRPC requer o pacote [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .

### <a name="configure-grpc"></a>Configurar o gRPC

Em *Startup.cs*:

* gRPC é habilitado com o `AddGrpc` método.
* Cada serviço gRPC é adicionado ao pipeline de roteamento por meio do `MapGrpcService` método.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais. Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.

## <a name="server-options"></a>Opções de servidor

os serviços gRPCs podem ser hospedados por todos os servidores de ASP.NET Core internos.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;O IIS requer o .NET 5 e o Windows 10 Build 20241 ou posterior.

&Dagger;HTTP.sys requer o .NET 5 e o Windows 10 Build 19529 ou posterior.

Para obter mais informações sobre como escolher o servidor certo para um aplicativo ASP.NET Core, consulte <xref:fundamentals/servers/index> .

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) é um servidor Web de plataforma cruzada para ASP.NET Core. O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados em HTTP.sys como o compartilhamento de porta.

Kestrel pontos de extremidade gRPC:

* Exigir HTTP/2.
* Deve ser protegido com [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).

### <a name="http2"></a>HTTP/2

gRPC requer HTTP/2. gRPC para ASP.NET Core valida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) é `HTTP/2` .

O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel/http2) na maioria dos sistemas operacionais modernos. Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.

### <a name="tls"></a>TLS

Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com TLS. No desenvolvimento, um ponto de extremidade protegido com TLS é criado automaticamente `https://localhost:5001` quando o certificado de desenvolvimento de ASP.NET Core está presente. Nenhuma configuração é necessária. Um `https` prefixo verifica se o ponto de extremidade Kestrel está usando TLS.

Em produção, o TLS deve ser configurado explicitamente. No exemplo a seguir *appsettings.json* , um ponto de extremidade http/2 protegido com TLS é fornecido:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Negociação de protocolo

O TLS é usado para mais do que proteger a comunicação. O handshake [ALPN (negociação do protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto de extremidade dá suporte a vários protocolos. Essa negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.

Se um ponto de extremidade HTTP/2 estiver configurado sem TLS, os [protocolos de escuta](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) do ponto de extremidade deverão ser definidos como `HttpProtocols.Http2` . Um ponto de extremidade com vários protocolos (por exemplo, `HttpProtocols.Http1AndHttp2` ) não pode ser usado sem TLS porque não há negociação. Todas as conexões com o ponto de extremidade não seguro padrão para HTTP/1.1 e chamadas gRPC falham.

Para obter mais informações sobre como habilitar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel/endpoints).

> [!NOTE]
> O macOS não é compatível com gRPC do ASP.NET Core com TLS. É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS. Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="iis"></a>IIS

O [serviços de informações da Internet (IIS)](xref:host-and-deploy/iis/index) é um servidor Web flexível, seguro e gerenciável para hospedar aplicativos Web, incluindo ASP.NET Core. O .NET 5 e o Windows 10 Build 20241 ou posterior são necessários para hospedar serviços gRPC com o IIS.

O IIS deve ser configurado para usar TLS e HTTP/2. Para obter mais informações, consulte <xref:host-and-deploy/iis/protocols>.

## <a name="httpsys"></a>HTTP.sys

O [HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor Web para ASP.NET Core executado apenas no Windows. O .NET 5 e o Windows 10 Build 19529 ou posterior são necessários para hospedar serviços gRPCs com HTTP.sys.

HTTP.sys deve ser configurado para usar TLS e HTTP/2. Para obter mais informações, consulte  [ suporte ao servidor Web doHTTP.sys http/2](xref:fundamentals/servers/httpsys#http2-support).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) é um servidor Web de plataforma cruzada para ASP.NET Core. O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados em HTTP.sys como o compartilhamento de porta.

Kestrel pontos de extremidade gRPC:

* Exigir HTTP/2.
* Deve ser protegido com [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).

### <a name="http2"></a>HTTP/2

gRPC requer HTTP/2. gRPC para ASP.NET Core valida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) é `HTTP/2` .

O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos. Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.

### <a name="tls"></a>TLS

Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com TLS. No desenvolvimento, um ponto de extremidade protegido com TLS é criado automaticamente `https://localhost:5001` quando o certificado de desenvolvimento de ASP.NET Core está presente. Nenhuma configuração é necessária. Um `https` prefixo verifica se o ponto de extremidade Kestrel está usando TLS.

Em produção, o TLS deve ser configurado explicitamente. No exemplo a seguir *appsettings.json* , um ponto de extremidade http/2 protegido com TLS é fornecido:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Negociação de protocolo

O TLS é usado para mais do que proteger a comunicação. O handshake [ALPN (negociação do protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto de extremidade dá suporte a vários protocolos. Essa negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.

Se um ponto de extremidade HTTP/2 estiver configurado sem TLS, os [protocolos de escuta](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deverão ser definidos como `HttpProtocols.Http2` . Um ponto de extremidade com vários protocolos (por exemplo, `HttpProtocols.Http1AndHttp2` ) não pode ser usado sem TLS porque não há negociação. Todas as conexões com o ponto de extremidade não seguro padrão para HTTP/1.1 e chamadas gRPC falham.

Para obter mais informações sobre como habilitar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> O macOS não é compatível com gRPC do ASP.NET Core com TLS. É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS. Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a>Integração com APIs de ASP.NET Core

os serviços gRPCs têm acesso completo aos recursos de ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (di) e [registro em log](xref:fundamentals/logging/index). Por exemplo, a implementação do serviço pode resolver um serviço de agente do contêiner DI por meio do Construtor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (singleton, com escopo ou transitório).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Resolver HttpContext em métodos gRPC

A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, o host, o cabeçalho e os trailers. O acesso é por meio do `ServerCallContext` argumento passado para cada método gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.net. O `GetHttpContext` método de extensão fornece acesso completo à `HttpContext` representação da mensagem http/2 subjacente em APIs ASP.net:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
