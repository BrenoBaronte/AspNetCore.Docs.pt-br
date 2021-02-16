---
title: Ferramentas para ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre as ferramentas disponíveis para criar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
ms.openlocfilehash: 6b61d9a4645d273b0c78fae0388d569771c43a2d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536240"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Ferramentas para ASP.NET Core Blazor

::: zone pivot="windows"

1. Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

1. Criar um novo projeto.

1. Selecione **Blazor aplicativo**. Selecione **Avançar**.

1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** . Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** . Selecione **Criar**.

   Para uma experiência hospedada Blazor WebAssembly , marque a caixa de seleção **ASP.NET Core hospedado** .

   Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.

Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

Ao executar um Blazor WebAssembly aplicativo hospedado, execute o aplicativo do projeto da solução **`Server`** .

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

   Para uma experiência hospedada Blazor WebAssembly , adicione a opção Hosted ( `-ho` ou `--hosted` ) ao comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Abra a pasta `WebApplication1` no Visual Studio Code.

1. O IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim**.

   **Blazor WebAssemblyConfiguração de tarefa e inicialização hospedada**

   Para soluções hospedadas Blazor WebAssembly , adicione (ou mova) a `.vscode` pasta com `launch.json` e `tasks.json` arquivos para a pasta pai da solução, que é a pasta que contém os nomes de pasta de projeto típicos de `Client` , `Server` e `Shared` . Atualize ou confirme se a configuração nos `launch.json` arquivos e `tasks.json` executa um aplicativo hospedado Blazor WebAssembly do **`Server`** projeto.

   **`.vscode/launch.json`** ( `launch` configuração):

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   Na configuração anterior para o diretório de trabalho atual ( `cwd` ), o `{SERVER APP FOLDER}` espaço reservado é a **`Server`** pasta do projeto, normalmente " `Server` ".

   Se o Microsoft Edge for usado e o Google Chrome não estiver instalado no sistema, adicione uma propriedade adicional de `"browser": "edge"` à configuração.

   Exemplo de uma pasta de projeto do `Server` e que gera o Microsoft Edge como o navegador para execuções de depuração em vez do navegador padrão Google Chrome:

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   **`.vscode/tasks.json`**(argumentos de [ `dotnet` comando](/dotnet/core/tools/dotnet) ):

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   No argumento anterior:

   * O `{SERVER APP FOLDER}` espaço reservado é a **`Server`** pasta do projeto, normalmente " `Server` ".
   * O `{PROJECT NAME}` espaço reservado é o nome do aplicativo, normalmente com base no nome da solução seguido por " `.Server` " em um aplicativo gerado a partir do Blazor modelo de projeto.

   O exemplo a seguir do [tutorial para usar SignalR com um Blazor WebAssembly aplicativo](xref:tutorials/signalr-blazor) usa um nome de pasta de projeto de `Server` e um nome de projeto de `BlazorWebAssemblySignalRApp.Server` :

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

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

1. Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.

1. Na barra lateral, selecione **aplicativo Web e de console**  >  .

   Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** . Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** . Selecione **Avançar**.

   Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Confirme se a **autenticação** está definida como **sem autenticação**. Selecione **Avançar**.

1. Para uma experiência hospedada Blazor WebAssembly , marque a caixa de seleção **ASP.NET Core hospedado** .

1. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` . Selecione **Criar**.

1. Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.

Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue. As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado. Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

Ao executar um Blazor WebAssembly aplicativo hospedado, execute o aplicativo do projeto da solução **`Server`** .

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a>Usar Visual Studio Code para o desenvolvimento de plataforma cruzada Blazor

O [Visual Studio Code](https://code.visualstudio.com/) é um IDE (ambiente de desenvolvimento integrado) de plataforma cruzada de software livre que pode ser usado para desenvolver Blazor aplicativos. Use a CLI do .NET para criar um novo Blazor aplicativo para desenvolvimento com o Visual Studio Code. Para obter mais informações, consulte a [versão do Linux deste artigo](?pivots=linux).

## <a name="blazor-template-options"></a>Blazor opções de modelo

A Blazor estrutura fornece modelos para a criação de novos aplicativos para cada um dos dois Blazor modelos de hospedagem. Os modelos são usados para criar novos Blazor projetos e soluções, independentemente das ferramentas que você selecionar para Blazor desenvolvimento (Visual Studio, Visual Studio para Mac, Visual Studio Code ou a CLI do .net):

* Blazor WebAssembly modelo de projeto: `blazorwasm`
* Blazor Server modelo de projeto: `blazorserver`

Para obter mais informações sobre os Blazor modelos de hospedagem do, consulte <xref:blazor/hosting-models> .

As opções de modelo estão disponíveis passando a opção de ajuda ( `-h` ou `--help` ) para o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando da CLI em um shell de comando:

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
