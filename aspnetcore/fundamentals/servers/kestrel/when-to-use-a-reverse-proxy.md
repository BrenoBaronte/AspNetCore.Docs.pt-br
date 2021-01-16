---
title: Quando usar um proxy reverso com o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba mais sobre quando usar um proxy reverso na frente do Kestrel, o servidor Web de plataforma cruzada para ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253909"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Quando usar o Kestrel com um proxy reverso

O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/). Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.

Kestrel usado como um servidor Web de borda (voltado para a Internet):

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](_static/kestrel-to-internet2.png)

Kestrel usado em uma configuração de proxy reverso:

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](_static/kestrel-to-internet.png)

A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.

Quando Kestrel é usado como um servidor de borda sem um servidor proxy reverso, não há suporte para o compartilhamento do mesmo endereço IP e porta entre vários processos. Quando Kestrel é configurado para escutar em uma porta, o Kestrel manipula todo o tráfego para essa porta independentemente dos `Host` cabeçalhos de solicitações. Um proxy reverso que pode compartilhar portas pode encaminhar solicitações para Kestrel em um IP e uma porta exclusivos.

Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.

Um proxy reverso:

* Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.
* Fornece uma camada adicional de configuração e proteção.
* Pode ser integrado melhor à infraestrutura existente.
* Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS). Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.

> [!WARNING]
> A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](xref:fundamentals/servers/kestrel/host-filtering).

## <a name="additional-resources"></a>Recursos adicionais

<xref:host-and-deploy/proxy-load-balancer>

