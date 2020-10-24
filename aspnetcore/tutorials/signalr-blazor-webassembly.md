---
title: Usar ASP.NET Core SignalR com Blazor WebAssembly
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 6086c422b2d0598af309bbf8b900b9e03a3fe147
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491581"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a>Usar ASP.NET Core SignalR com Blazor WebAssembly

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor WebAssembly . Você aprenderá como:

> [!div class="checklist"]
> * Criar um Blazor WebAssembly projeto de aplicativo hospedado
> * Adicionar a SignalR biblioteca de cliente
> * Adicionar um SignalR Hub
> * Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub
> * Adicionar Razor código de componente para o chat

No final deste tutorial, você terá um aplicativo de chat em funcionamento.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* [Visual Studio 2019 16,8 ou posterior (em versão prévia)](https://visualstudio.microsoft.com/vs/preview/) com a **ASP.net e** a carga de trabalho de desenvolvimento Web
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* [Visual Studio para Mac versão 8,8 ou posterior (em visualização)](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [Visual Studio para Mac versão 8,6 ou posterior](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Criar um Blazor WebAssembly projeto de aplicativo hospedado

Siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.

::: moniker-end

1. Criar um novo projeto.

1. Selecione ** Blazor aplicativo** e selecione **Avançar**.

1. Digite `BlazorSignalRApp` o campo **nome do projeto** . Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Escolha o modelo de ** Blazor WebAssembly aplicativo** .

1. Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .

1. Selecione **Criar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Em Visual Studio Code, abra a pasta do projeto do aplicativo.

1. Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**. Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:

1. Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.

1. Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.

1. Escolha o modelo de ** Blazor WebAssembly aplicativo** . Selecione **Avançar**.

1. Confirme se a **autenticação** está definida como **sem autenticação**. Marque a caixa de seleção **ASP.NET Core hospedado** . Selecione **Avançar**.

1. No campo **nome do projeto** , nomeie o aplicativo `BlazorSignalRApp` . Selecione **Criar**.

   Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue. As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.

1. Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Adicionar a SignalR biblioteca de cliente

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **instalar**.

1. Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.

1. Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **Adicionar pacote**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute os seguintes comandos:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a>Adicionar um SignalR Hub

No `BlazorSignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Adicionar serviços e um ponto de extremidade para o SignalR Hub

1. No projeto `BlazorSignalRApp.Server`, abra o arquivo `Startup.cs`.

1. Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. Em `Startup.Configure`:

   * Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.
   * Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>Adicionar Razor código de componente para o chat

1. No projeto `BlazorSignalRApp.Client`, abra o arquivo `Pages/Index.razor`.

1. Substitua a marcação pelo código a seguir:

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Executar o aplicativo

1. Siga as orientações para suas ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Em **Gerenciador de soluções**, selecione o `BlazorSignalRApp.Server` projeto. Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor ( `.vscode/launch.json` ), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , selecione o `BlazorSignalRApp.Server` projeto. Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Em um shell de comando, execute os seguintes comandos:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um Blazor WebAssembly projeto de aplicativo hospedado
> * Adicionar a SignalR biblioteca de cliente
> * Adicionar um SignalR Hub
> * Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub
> * Adicionar Razor código de componente para o chat

Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [Autenticação de token de portador com Identity eventos de servidor, WebSocket e Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>
* [SignalR negociação entre origens para autenticação](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
