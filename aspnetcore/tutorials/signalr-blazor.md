---
title: Usar ASP.NET Core SignalR com Blazor
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: f4e51b39c4c3b0c444b08025e9bd74eec0747541
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536348"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a>Usar ASP.NET Core SignalR com Blazor

Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor . Você aprenderá como:

> [!div class="checklist"]
> * Criar um Blazor projeto
> * Adicionar a SignalR biblioteca de cliente
> * Adicionar um SignalR Hub
> * Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub
> * Adicionar Razor código de componente para o chat

No final deste tutorial, você terá um aplicativo de chat em funcionamento.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,8 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [Visual Studio para Mac versão 8,8 ou posterior](https://visualstudio.microsoft.com/vs/mac/)
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

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a>Criar um Blazor WebAssembly aplicativo hospedado

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

1. Selecione **Blazor aplicativo** e selecione **Avançar**.

1. Digite `BlazorWebAssemblySignalRApp` o campo **nome do projeto** . Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Escolha o modelo de **Blazor WebAssembly aplicativo** .

1. Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .

1. Selecione **Criar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   A `-ho|--hosted` opção cria uma solução hospedada Blazor WebAssembly . Para obter informações sobre como configurar os ativos de VS Code na `.vscode` pasta, consulte as diretrizes do sistema operacional **Linux** em <xref:blazor/tooling> .

   A `-o|--output` opção cria uma pasta para a solução. Se você tiver criado uma pasta para a solução e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar a solução.

1. Em Visual Studio Code, abra a pasta do projeto do aplicativo.

1. Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**. Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:

1. Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.

1. Na barra lateral, selecione **aplicativo Web e de console**  >  .

1. Escolha o modelo de **Blazor WebAssembly aplicativo** . Selecione **Avançar**.

1. Confirme se a **autenticação** está definida como **sem autenticação**. Marque a caixa de seleção **ASP.NET Core hospedado** . Selecione **Avançar**.

1. No campo **nome do projeto** , nomeie o aplicativo `BlazorWebAssemblySignalRApp` . Selecione **Criar**.

   Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue. As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.

1. Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

A `-ho|--hosted` opção cria uma solução hospedada Blazor WebAssembly .

A `-o|--output` opção cria uma pasta para a solução. Se você tiver criado uma pasta para a solução e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar a solução.

---

## <a name="add-the-signalr-client-library"></a>Adicionar a SignalR biblioteca de cliente

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote. Defina a versão para corresponder à estrutura compartilhada do aplicativo. Selecione **Instalar**.

1. Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute o seguinte comando:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.

1. Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote. Defina a versão para corresponder à estrutura compartilhada do aplicativo. Selecione **Adicionar pacote**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando da pasta da solução, execute o seguinte comando:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Adicione o pacote System. Text. transcodifications. Web

*Esta seção se aplica somente a aplicativos para ASP.NET Core versão 3. x.*

Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x em um aplicativo ASP.NET Core 3. x, o `BlazorWebAssemblySignalRApp.Server` projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) . O problema subjacente será resolvido em uma versão futura de patch do .NET 5. Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).

Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao `BlazorWebAssemblySignalRApp.Server` projeto da solução ASP.NET Core 3,1 hospedada Blazor , siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote. Selecione a versão do pacote que corresponde à estrutura compartilhada em uso. Selecione **Instalar**.

1. Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.

1. Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote, selecione a versão correta do pacote que corresponde à estrutura compartilhada em uso e selecione **Adicionar pacote**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando da pasta da solução, execute o seguinte comando:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Adicionar um SignalR Hub

No `BlazorWebAssemblySignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Adicionar serviços e um ponto de extremidade para o SignalR Hub

1. No projeto `BlazorWebAssemblySignalRApp.Server`, abra o arquivo `Startup.cs`.

1. Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. Em `Startup.Configure`:

   * Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.
   * Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. Em `Startup.Configure`:

   * Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.
   * Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Adicionar Razor código de componente para o chat

1. No projeto `BlazorWebAssemblySignalRApp.Client`, abra o arquivo `Pages/Index.razor`.

::: moniker range=">= aspnetcore-5.0"

1. Substitua a marcação pelo código a seguir:

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Substitua a marcação pelo código a seguir:

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Execute o aplicativo

Siga as orientações para suas ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Em **Gerenciador de soluções**, selecione o `BlazorWebAssemblySignalRApp.Server` projeto. Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para obter informações sobre como configurar os ativos de VS Code na `.vscode` pasta, consulte as diretrizes do sistema operacional **Linux** em <xref:blazor/tooling> .

1. Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , selecione o `BlazorWebAssemblySignalRApp.Server` projeto. Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Em um shell de comando da pasta da solução, execute os seguintes comandos:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a>Criar um Blazor Server aplicativo

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

1. Selecione **Blazor aplicativo** e selecione **Avançar**.

1. Digite `BlazorServerSignalRApp` o campo **nome do projeto** . Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Escolha o modelo de **Blazor Server aplicativo** .

1. Selecione **Criar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   A `-o|--output` opção cria uma pasta para o projeto. Se você tiver criado uma pasta para o projeto e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar o projeto.

1. Em Visual Studio Code, abra a pasta do projeto do aplicativo.

1. Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**. Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` . Para obter informações sobre como configurar os ativos de VS Code na `.vscode` pasta, consulte as diretrizes do sistema operacional **Linux** em <xref:blazor/tooling> .

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:

1. Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.

1. Na barra lateral, selecione **aplicativo Web e de console**  >  .

1. Escolha o modelo de **Blazor Server aplicativo** . Selecione **Avançar**.

1. Confirme se a **autenticação** está definida como **sem autenticação**. Selecione **Avançar**.

1. No campo **nome do projeto** , nomeie o aplicativo `BlazorServerSignalRApp` . Selecione **Criar**.

   Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue. As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.

1. Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

A `-o|--output` opção cria uma pasta para o projeto. Se você tiver criado uma pasta para o projeto e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar o projeto.

---

## <a name="add-the-signalr-client-library"></a>Adicionar a SignalR biblioteca de cliente

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote. Defina a versão para corresponder à estrutura compartilhada do aplicativo. Selecione **Instalar**.

1. Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute o seguinte comando:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.

1. Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote. Defina a versão para corresponder à estrutura compartilhada do aplicativo. Selecione **Adicionar pacote**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando da pasta do projeto, execute o seguinte comando:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Adicione o pacote System. Text. transcodifications. Web

*Esta seção se aplica somente a aplicativos para ASP.NET Core versão 3. x.*

Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x em um aplicativo ASP.NET Core 3. x, o projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) . O problema subjacente será resolvido em uma versão futura de patch do .NET 5. Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).

Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao projeto, siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote. Selecione a versão do pacote que corresponde à estrutura compartilhada em uso. Selecione **Instalar**.

1. Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .

1. Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.

1. Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote, selecione a versão correta do pacote que corresponde à estrutura compartilhada em uso e selecione **Adicionar pacote**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando da pasta do projeto, execute o seguinte comando:

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Adicionar um SignalR Hub

Crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Adicionar serviços e um ponto de extremidade para o SignalR Hub

1. Abra o arquivo `Startup.cs` .

1. Adicione os namespaces para <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> e a `ChatHub` classe à parte superior do arquivo:

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Adicionar serviços de middleware de compactação de resposta a `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. Em `Startup.Configure`:

   * Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.
   * Entre os pontos de extremidade para mapear o Blazor Hub e o fallback do lado do cliente, adicione um EndPoint para o Hub.

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Adicionar serviços de middleware de compactação de resposta a `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. Em `Startup.Configure`:

   * Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.
   * Entre os pontos de extremidade para mapear o Blazor Hub e o fallback do lado do cliente, adicione um EndPoint para o Hub.

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Adicionar Razor código de componente para o chat

1. Abra o arquivo `Pages/Index.razor` .

::: moniker range=">= aspnetcore-5.0"

1. Substitua a marcação pelo código a seguir:

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Substitua a marcação pelo código a seguir:

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Execute o aplicativo

Siga as orientações para suas ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Em um shell de comando da pasta do projeto, execute os seguintes comandos:

   ```dotnetcli
   dotnet run
   ```

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um Blazor projeto
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
* [SignalR negociação entre origens para autenticação](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
