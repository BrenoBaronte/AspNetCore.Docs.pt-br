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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552464"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Criar um novo projeto.
1. Selecione **serviço de trabalho**. Selecione **Avançar**.
1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Selecione **Criar**.
1. Na caixa de diálogo **criar um novo serviço de trabalho** , selecione **criar**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Criar um novo projeto.
1. Selecione **aplicativo** em **.NET Core** na barra lateral.
1. Selecione **trabalhador** em **ASP.NET Core**. Selecione **Avançar**.
1. Selecione **.NET Core 3,0** ou posterior para a **estrutura de destino**. Selecione **Avançar**.
1. Forneça um nome no campo **nome do projeto** . Selecione **Criar**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use o modelo de Serviço de Trabalho (`worker`) com o comando [dotnet novo](/dotnet/core/tools/dotnet-new) em um shell de comando. No exemplo a seguir, um aplicativo de Serviço de Trabalho é criado com o nome `ContosoWorker`. Uma pasta para o aplicativo `ContosoWorker` é criada automaticamente quando o comando é executado.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
