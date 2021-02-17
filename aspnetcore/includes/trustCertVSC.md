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
ms.openlocfilehash: df4a9a7db8097ea765b2d0f404305b771f994ddf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551933"
---
* Confie no certificado de desenvolvimento HTTPS executando o seguinte comando:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  O comando anterior não funciona no Linux. Veja a documentação da distribuição do Linux para confiar em um certificado.

  O comando anterior exibe a caixa de diálogo a seguir:

  ![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

* Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.

  Para obter mais informações, veja [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]