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
ms.openlocfilehash: 1b045d437d1a16eabc0ab41573c8b66d9c4bb77e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552172"
---
A estrutura usa como padrão o modo de logon pop-up e volta a redirecionar o modo de logon se um pop-up não puder ser aberto. Configure MSAL para usar o modo de logon de redirecionamento definindo a `LoginMode` propriedade de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> como `redirect` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

A configuração padrão é `popup` , e o valor da cadeia de caracteres não diferencia maiúsculas de minúsculas.
