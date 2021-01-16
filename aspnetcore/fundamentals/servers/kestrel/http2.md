---
title: Usar HTTP/2 com o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba como usar o HTTP/2 com o Kestrel, o servidor Web multiplataforma para ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253915"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>Usar HTTP/2 com o servidor Web ASP.NET Core Kestrel

O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:

* Sistema operacional&dagger;
  * Windows Server 2016/Windows 10 ou posterior&Dagger;
  * Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)
* Estrutura de destino: .NET Core 2.2 ou posterior
* Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)
* Conexão TLS 1.2 ou posterior

&dagger;O HTTP/2 será compatível com macOS em uma versão futura.
&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1. O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada. Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.

Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) relatará `HTTP/2`.

A partir do .NET Core 3,0, o HTTP/2 é habilitado por padrão. Para obter mais informações sobre a configuração, consulte as seções [limites de Kestrel http/2](xref:fundamentals/servers/kestrel/options#http2-limits) e [ListenerOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) .

## <a name="advanced-http2-features"></a>Recursos avançados de HTTP/2

Recursos adicionais de HTTP/2 no Kestrel dão suporte a gRPC, incluindo suporte para trailers de resposta e envio de quadros de redefinição.

### <a name="trailers"></a>Trailers

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Redefinir

[!INCLUDE[](~/includes/reset.md)]
