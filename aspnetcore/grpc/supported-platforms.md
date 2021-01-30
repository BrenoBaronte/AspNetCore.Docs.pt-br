---
title: gRPC para plataformas com suporte do .NET
author: jamesnk
description: Saiba mais sobre as plataformas com suporte para o gRPC no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 92ca38875c6618c8630a66af16548d32bc469a62
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057708"
---
# <a name="grpc-for-net-supported-platforms"></a>gRPC para plataformas com suporte do .NET

Por [James Newton – King](https://twitter.com/jamesnk)

Este artigo aborda os requisitos e as plataformas com suporte para o uso do gRPC com o .NET.

o gRPC foi projetado para usar HTTP/2 para alguns de seus recursos mais avançados. Não há suporte para HTTP/2 em todos os lugares que podem impedir o uso de gRPC. Por causa disso, há um segundo formato de transmissão compatível com HTTP/1.1 para enviar chamadas gRPC entre clientes e servidores:

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sobre HTTP/2 é como o gRPC normalmente é usado.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifica o protocolo gRPC para que seja compatível com HTTP/1.1. o gRPC-Web pode ser usado em mais lugares, notadamente que é possível chamá-lo por aplicativos de navegador. Dois recursos avançados do gRPC não têm mais suporte: streaming de cliente e streaming bidirecional.

o gRPC para .NET dá suporte a formatos de fios. Para obter informações sobre como configurar o gRPC-Web, consulte <xref:grpc/browser> .

## <a name="device-requirements"></a>Requisitos do dispositivo

o gRPC para .NET dá suporte a qualquer dispositivo compatível com o .NET Core.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Navegadores&Dagger;

&dagger;ASP.NET Core aplicativos hospedados no macOS não dão suporte a HTTPS. Os clientes do gRPC no macOS ainda podem usar HTTPS ao chamar serviços remotos.

&Dagger;Blazor WebAssembly os aplicativos podem chamar serviços gRPCs com gRPC-Web.

## <a name="aspnet-core-server-requirements"></a>Requisitos do ASP.NET Core Server

os serviços gRPCs podem ser hospedados em todos os servidores de ASP.NET Core internos.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&dagger;

&dagger;O IIS e o HTTP.sys exigem o .NET 5 e o Windows 10 Build 20241 ou posterior.

Para obter mais informações, consulte <xref:grpc/aspnetcore>.

## <a name="net-version-requirements"></a>Requisitos de versão do .NET

o gRPC para .NET dá suporte ao .NET Core 3 e ao .NET 5 ou posterior.

> [!div class="checklist"]
>
> * .NET 5 ou posterior
> * .NET Core 3

o gRPC para .NET não dá suporte à execução em .NET Framework e Xamarin. [GRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) é uma biblioteca de terceiros que dá suporte a .NET Framework e Xamarin. o gRPC C-Core não tem suporte da Microsoft.

## <a name="azure-services"></a>Serviços do Azure

> [!div class="checklist"]
>
> * [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Serviço de Azure App](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;O serviço de Azure App não dá suporte à Hospedagem de gRPC sobre HTTP/2. gRPC-Web é uma alternativa compatível.

O trabalho está em andamento para melhorar o suporte para gRPC com HTTP/2 no serviço Azure App. Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/9020).

## <a name="additional-resources"></a>Recursos adicionais

* [gRPC C# Core – biblioteca](https://grpc.io/docs/languages/csharp/quickstart/)
