---
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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552553"
---
O middleware de cabeçalhos encaminhados deve ser executado antes de outro middleware. Essa ordenação garantirá que o middleware conte com informações de cabeçalhos encaminhadas que podem consumir os valores de cabeçalho para processamento. Para executar o middleware de cabeçalhos encaminhados após o diagnóstico e o middleware de tratamento de erros, consulte [ordem de middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#fhmo).