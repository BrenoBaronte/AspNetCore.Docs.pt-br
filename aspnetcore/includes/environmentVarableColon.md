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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536266"
---
O `:` separador não funciona com chaves hierárquicas de variáveis de ambiente em todas as plataformas. `__`, o sublinhado duplo é:

* Compatível com todas as plataformas. Por exemplo, o `:` separador não tem suporte do [bash](https://linuxhint.com/bash-environment-variables/), mas sim `__` .
* Substituído automaticamente por um `:`