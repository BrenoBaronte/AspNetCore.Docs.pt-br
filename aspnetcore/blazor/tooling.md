---
title: Ferramentas para ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre as ferramentas disponíveis para criar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 500342ac979efdee824ac0d4b5757ca9804f3b30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054808"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>Ferramentas para ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

::: zone pivot="windows"

1. Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

1. Criar um novo projeto.

1. Selecione **Blazor aplicativo** . Selecione **Avançar** .

1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar** .

1. Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** . Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** . Selecione **Criar** .

   Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.

Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

::: zone-end

::: zone pivot="linux"

1. Instale a versão mais recente do [SDK do .NET Core](https://dotnet.microsoft.com/download). Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet --version
   ```

1. Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com).

1. Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Para obter uma Blazor WebAssembly experiência, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Abra a pasta `WebApplication1` no Visual Studio Code.

1. O IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim** na barra superior.

1. Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.

## <a name="trust-a-development-certificate"></a>Confiar em um certificado de desenvolvimento

Não há uma maneira centralizada de confiar em um certificado no Linux. Normalmente, uma das seguintes abordagens é adotada:

* Exclua a URL do aplicativo na lista de exclusões do navegador.
* Confiar em todos os certificados autoassinados para `localhost` .
* Adicione o certificado à lista de certificados confiáveis no navegador.

Para obter mais informações, consulte a orientação fornecida pelo fabricante do navegador e distribuição do Linux.

::: zone-end

::: zone pivot="macos"

1. Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar** .

1. Na barra lateral, selecione **aplicativo Web e de console**  >  **App** .

   Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** . Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** . Selecione **Avançar** .

   Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Confirme se a **autenticação** está definida como **sem autenticação** . Selecione **Avançar** .

1. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` . Selecione **Criar** .

1. Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador* . Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador* .

Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue. As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado. Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

::: zone-end
