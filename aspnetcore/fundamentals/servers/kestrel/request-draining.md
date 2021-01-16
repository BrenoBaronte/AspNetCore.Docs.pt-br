---
title: Solicitação de descarga com o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba mais sobre a solicitação de descarga com o Kestrel, o servidor Web multiplataforma para ASP.NET Core.
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253913"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>Solicitação de descarga com o servidor Web ASP.NET Core Kestrel

A abertura de conexões HTTP é demorada. Para HTTPS, ele também consome muitos recursos. Portanto, o Kestrel tenta reutilizar conexões de acordo com o protocolo HTTP/1.1. Um corpo de solicitação deve ser totalmente consumido para permitir que a conexão seja reutilizada. O aplicativo nem sempre consome o corpo da solicitação, como solicitações HTTP POST, em que o servidor retorna uma resposta redirecionada ou 404. No caso de redirecionamento HTTP POST:

* O cliente pode já ter enviado parte dos dados de POSTAgem.
* O servidor grava a resposta 301.
* A conexão não pode ser usada para uma nova solicitação até que os dados de POSTAgem do corpo da solicitação anterior tenham sido totalmente lidos.
* Kestrel tenta drenar o corpo da solicitação. Drenar o corpo da solicitação significa ler e descartar os dados sem processá-los.

O processo de descarga faz uma compensação entre permitir que a conexão seja reutilizada e o tempo necessário para drenar os dados restantes:

* O descarregamento tem um tempo limite de cinco segundos, o que não é configurável.
* Se todos os dados especificados pelo `Content-Length` `Transfer-Encoding` cabeçalho ou não tiverem sido lidos antes do tempo limite, a conexão será fechada.

Às vezes, você pode querer encerrar a solicitação imediatamente, antes ou depois de gravar a resposta. Por exemplo, os clientes podem ter limites de dados restritivos. Limitar os dados carregados pode ser uma prioridade. Nesses casos, para encerrar uma solicitação, chame [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) de um controlador, Razor página ou middleware.

Há limitações para chamar `Abort` :

* A criação de novas conexões pode ser lenta e dispendiosa.
* Não há nenhuma garantia de que o cliente leu a resposta antes que a conexão seja fechada.
* `Abort`A chamada deve ser rara e reservada para casos de erro graves, não erros comuns.
  * Só chame `Abort` quando um problema específico precisar ser resolvido. Por exemplo, chame `Abort` se clientes mal-intencionados estão tentando postar dados ou quando há um bug no código de cliente que causa grandes ou várias solicitações.
  * Não chame `Abort` situações de erro comuns, como HTTP 404 (não encontrado).

Chamar [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes `Abort` de chamar garante que o servidor concluiu a gravação da resposta. No entanto, o comportamento do cliente não é previsível e pode não ler a resposta antes que a conexão seja anulada.

Esse processo é diferente para HTTP/2 porque o protocolo dá suporte à anulação de fluxos de solicitação individuais sem fechar a conexão. O tempo limite de descarga de cinco segundos não se aplica. Se houver algum dado de corpo de solicitação não lido depois de concluir uma resposta, o servidor enviará um quadro RST HTTP/2. Quadros de dados de corpo de solicitação adicionais são ignorados.

Se possível, é melhor para os clientes usarem o cabeçalho de solicitação [esperado: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e aguarde até que o servidor responda antes de começar a enviar o corpo da solicitação. Isso dá ao cliente uma oportunidade de examinar a resposta e anular antes de enviar dados desnecessários.
