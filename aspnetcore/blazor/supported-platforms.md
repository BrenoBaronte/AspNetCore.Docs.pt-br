---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280720"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor plataformas com suporte

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly e Blazor Server têm suporte nos navegadores mostrados na tabela a seguir.

| Navegador                          | Versão         |
| -------------------------------- | --------------- |
| Apple Safari, incluindo iOS      | Atualizados&dagger; |
| Google Chrome, incluindo Android | Atualizados&dagger; |
| Microsoft Edge                   | Atualizados&dagger; |
| Mozilla Firefox                  | Atualizados&dagger; |  

&dagger;*Atual* refere-se à versão mais recente do navegador.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Navegador                          | Versão               |
| -------------------------------- | --------------------- |
| Apple Safari, incluindo iOS      | Atualizados&dagger;       |
| Google Chrome, incluindo Android | Atualizados&dagger;       |
| Microsoft Edge                   | Atualizados&dagger;       |
| Microsoft Internet Explorer      | Sem suporte&Dagger; |
| Mozilla Firefox                  | Atualizados&dagger;       |  

&dagger;*Atual* refere-se à versão mais recente do navegador.  
&Dagger;O Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

## Blazor Server

| Navegador                          | Versão         |
| -------------------------------- | --------------- |
| Apple Safari, incluindo iOS      | Atualizados&dagger; |
| Google Chrome, incluindo Android | Atualizados&dagger; |
| Microsoft Edge                   | Atualizados&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | Atualizados&dagger; |

&dagger;*Atual* refere-se à versão mais recente do navegador.  
&Dagger;Os suportes retroativos adicionais são necessários. Por exemplo, as promessas podem ser adicionadas por meio de um [`Polyfill.io`](https://polyfill.io/v3/) pacote.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
