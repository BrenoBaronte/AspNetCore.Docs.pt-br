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
* <span data-ttu-id="ebddd-101">Confie no certificado de desenvolvimento HTTPS executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ebddd-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="ebddd-102">O comando anterior não funciona no Linux.</span><span class="sxs-lookup"><span data-stu-id="ebddd-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="ebddd-103">Veja a documentação da distribuição do Linux para confiar em um certificado.</span><span class="sxs-lookup"><span data-stu-id="ebddd-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="ebddd-104">O comando anterior exibe a caixa de diálogo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ebddd-104">The preceding command displays the following dialog:</span></span>

  ![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

* <span data-ttu-id="ebddd-106">Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ebddd-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="ebddd-107">Para obter mais informações, veja [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="ebddd-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]