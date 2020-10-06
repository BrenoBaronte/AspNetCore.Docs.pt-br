---
title: ASP.NET Core SignalR plataformas com suporte
author: bradygaster
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
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
uid: signalr/supported-platforms
ms.openlocfilehash: a342dd787eceadd22ac26b57a3615a6b0b21f461
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754496"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>ASP.NET Core SignalR plataformas com suporte

## <a name="server-system-requirements"></a>Requisitos do sistema do servidor do

SignalR para ASP.NET Core dá suporte a qualquer plataforma de servidor que ASP.NET Core suporte.

## <a name="javascript-client"></a>Cliente JavaScript

O [cliente JavaScript](xref:signalr/javascript-client) é executado no NodeJS 8 e versões posteriores e nos seguintes navegadores:

| Navegador                          | Versão         |
| -------------------------------- | --------------- |
| Apple Safari, incluindo iOS      | Atualizados&dagger; |
| Google Chrome, incluindo Android | Atualizados&dagger; |
| Microsoft Edge                   | Atualizados&dagger; |
| Mozilla Firefox                  | Atualizados&dagger; |

&dagger;*Atual* refere-se à versão mais recente do navegador.

## <a name="net-client"></a>Cliente .NET

O [cliente .net](xref:signalr/dotnet-client) é executado em qualquer plataforma com suporte do ASP.NET Core. Por exemplo, [os desenvolvedores do xamarin SignalR podem usar](https://github.com/aspnet/Announcements/issues/305) o para criar aplicativos Android usando xamarin. Android 8.4.0.1 e posterior e aplicativos Ios usando xamarin. Ios 11.14.0.4 e posterior.

Se o servidor executar o IIS, o transporte do WebSockets exigirá o IIS 8,0 ou posterior no Windows Server 2012 ou posterior. Outros transportes têm suporte em todas as plataformas.

## <a name="java-client"></a>Cliente Java

O [cliente Java](xref:signalr/java-client) dá suporte a Java 8 e versões posteriores.

## <a name="unsupported-clients"></a>Clientes sem suporte

Os clientes a seguir estão disponíveis, mas são experimentais ou não oficiais. Atualmente, eles não têm suporte e podem nunca ser.

* [Cliente C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Cliente Swift](https://github.com/moozzyk/SignalR-Client-Swift)
