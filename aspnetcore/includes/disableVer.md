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
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551497"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Desabilitar verificação de conta padrão

Com os modelos padrão, o usuário é redirecionado para o `Account.RegisterConfirmation` onde ele pode selecionar um link para que a conta seja confirmada. O padrão `Account.RegisterConfirmation` é usado ***somente*** para teste; a verificação automática de conta deve ser desabilitada em um aplicativo de produção.

Para exigir uma conta confirmada e impedir o logon imediato no registro, defina `DisplayConfirmAccountLink = false` em */areas/ Identity /pages/Account/RegisterConfirmation.cshtml.cs*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]