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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551235"
---
## <a name="share-interop-code-in-a-class-library"></a>Compartilhar código de interoperabilidade em uma biblioteca de classes

O código de interoperabilidade JS pode ser incluído em uma biblioteca de classes, que permite que você compartilhe o código em um pacote NuGet.

A biblioteca de classes lida com a inserção de recursos JavaScript no assembly compilado. Os arquivos JavaScript são colocados na `wwwroot` pasta. As ferramentas cuidam da inserção dos recursos quando a biblioteca é criada.

O pacote NuGet criado é referenciado no arquivo de projeto do aplicativo da mesma forma que qualquer pacote NuGet é referenciado. Depois que o pacote é restaurado, o código do aplicativo pode chamar o JavaScript como se fosse o C#.

Para obter mais informações, consulte <xref:blazor/components/class-libraries>.
