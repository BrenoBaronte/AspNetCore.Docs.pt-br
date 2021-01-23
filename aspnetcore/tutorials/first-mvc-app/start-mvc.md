---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710747"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Introdução ao ASP.NET Core MVC

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Este é o primeiro tutorial de uma série que ensina ASP.NET Core desenvolvimento para a Web MVC com controladores e exibições.

No final da série, você terá um aplicativo que gerencia e exibe dados de filmes. Você aprenderá como:

> [!div class="checklist"]
> * Crie um aplicativo Web.
> * Adicionar e gerar o scaffolding de um modelo.
> * Trabalhar com um banco de dados.
> * Adicionar pesquisa e validação.

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>Criar um aplicativo Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Inicie o Visual Studio e selecione **Criar um projeto**.
* Na caixa de diálogo **criar um novo projeto** , selecione **ASP.NET Core aplicativo Web** > **Avançar**.
* Na caixa de diálogo **configurar seu novo projeto** , digite `MvcMovie` para **nome do projeto**. É importante nomear o projeto *MvcMovie*. A capitalização precisa corresponder a cada `namespace` correspondência quando o código é copiado.
* Selecione **Criar**.
* Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione:
  * **.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.
  * **ASP.NET Core aplicativo Web (Model-View-Controller)**.
  * **Criar**.

![Criar um novo aplicativo Web ASP.NET Core ](start-mvc/_static/mvcVS19v16.9.png)

Para obter abordagens alternativas para criar o projeto, consulte [criar um novo projeto no Visual Studio](/visualstudio/ide/create-new-project).

O Visual Studio usou o modelo de projeto padrão para o projeto MVC criado. O projeto criado:

* É um aplicativo de trabalho.
* É um projeto inicial básico.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

O tutorial pressupõe familiaridade com VS Code. Para obter mais informações, consulte [introdução ao vs Code](https://code.visualstudio.com/docs) e [Visual Studio Code ajuda](#visual-studio-code-help).

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere para o diretório ( `cd` ) que conterá o projeto.
* Execute o comando a seguir:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Se uma caixa de diálogo aparecer com **ativos necessários para compilação e depuração estiverem ausentes em ' MvcMovie '. Adicioná-los?**, selecione **Sim**

  * `dotnet new mvc -o MvcMovie`: Cria um novo projeto ASP.NET Core MVC na pasta *MvcMovie*
  * `code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >    >  **(Model-View-Controller)**  >  **Avançar**. Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >    >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* Na caixa de diálogo **configurar seu novo aplicativo Web** :

  * Confirme se a **autenticação** está definida como **sem autenticação**.
  * Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 5. x mais recente.
  * Selecione **Avançar**.

* Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Execute o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Selecione CTRL + F5 para executar o aplicativo sem o depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * Inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Executa o aplicativo.

  A barra de endereços mostra `localhost:port#` e não algo como `example.com`. O nome do host padrão para seu computador local é `localhost` . Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.

Iniciar o aplicativo sem depuração selecionando CTRL + F5 permite que você:

* Realize alterações de código.
* Salve o arquivo.
* Atualize rapidamente o navegador e veja as alterações no código.

Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:

![Menu Depurar](start-mvc/_static/debug_menu50.png)

Você pode depurar o aplicativo selecionando o botão **IIS Express**

![IIS Express](start-mvc/_static/iis_express50.png)

A imagem a seguir mostra o aplicativo:

![Página Inicial ou de Índice](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Selecione CTRL + F5 para executar sem o depurador.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * Inicia o [Kestrel](xref:fundamentals/servers/kestrel)
  * Inicia um navegador.
  * Navega para `https://localhost:5001` .

  A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`. O nome do host padrão para seu computador local é `localhost` . Localhost serve somente solicitações da Web do computador local.

Iniciar o aplicativo sem depuração selecionando CTRL + F5 permite que você:

* Realize alterações de código.
* Salve o arquivo.
* Atualize rapidamente o navegador e veja as alterações no código.

  ![Página Inicial ou de Índice](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.

  Visual Studio para Mac:

  * Inicia o servidor do [Kestrel](xref:fundamentals/servers/index#kestrel) .
  * Inicia um navegador.
  * Navega para `http://localhost:port` , em que *porta* é um número de porta escolhido aleatoriamente.

  [!INCLUDE[](~/includes/trustCertMac.md)]

  A barra de endereços mostra `localhost:port#` e não algo como `example.com`. O nome do host padrão para seu computador local é `localhost` . Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.

Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.

A imagem a seguir mostra o aplicativo:

![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.

> [!div class="step-by-step"]
> [Em seguida: adicionar um controlador](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Este é o primeiro tutorial de uma série que ensina ASP.NET Core desenvolvimento para a Web MVC com controladores e exibições.

No final da série, você terá um aplicativo que gerencia e exibe dados de filmes. Você aprenderá como:

> [!div class="checklist"]
> * Crie um aplicativo Web.
> * Adicionar e gerar o scaffolding de um modelo.
> * Trabalhar com um banco de dados.
> * Adicionar pesquisa e validação.

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Criar um aplicativo Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No Visual Studio, selecione **criar um novo projeto**.

* Selecione **ASP.NET Core aplicativo Web** a > **seguir**.

  ![Criar um novo projeto de aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**. É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.

  ![Configurar seu novo projeto](start-mvc/_static/config.png)

* Selecione **aplicativo Web (Model-View-Controller)**. Nas caixas suspensas, selecione **.NET Core** e **ASP.NET Core 3,1** e, em seguida, selecione **criar**.

  ![Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core ](start-mvc/_static/new_project30.png)

O Visual Studio usou o modelo de projeto padrão para o projeto MVC criado. O projeto criado:

* É um aplicativo de trabalho.
* É um projeto inicial básico.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

O tutorial pressupõe familiaridade com VS Code. Para obter mais informações, consulte [introdução ao vs Code](https://code.visualstudio.com/docs) e [Visual Studio Code ajuda](#visual-studio-code-help).

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios ( `cd` ) para uma pasta que conterá o projeto.
* Execute o comando a seguir:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**, selecione **Sim**.

  * `dotnet new mvc -o MvcMovie`: Cria um novo projeto ASP.NET Core MVC na pasta *MvcMovie*
  * `code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >    >  **(Model-View-Controller)**  >  **Avançar**. Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >    >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* Na caixa de diálogo **configurar seu novo aplicativo Web** :

  * Confirme se a **autenticação** está definida como **sem autenticação**.
  * Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 3. x mais recente.
  * Selecione **Avançar**.

* Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Execute o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Selecione CTRL + F5 para executar o aplicativo sem depuração.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * Inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Executa o aplicativo.

  A barra de endereços mostra `localhost:port#` e não algo como `example.com`. O nome do host padrão para seu computador local é `localhost` . Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.

Iniciar o aplicativo sem depuração selecionando CTRL + F5 permite que você:

* Realize alterações de código.
* Salve o arquivo.
* Atualize rapidamente o navegador e veja as alterações no código.

Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:

![Menu Depurar](start-mvc/_static/debug_menu.png)

Você pode depurar o aplicativo selecionando o botão **IIS Express**

![IIS Express](start-mvc/_static/iis_express.png)

A imagem a seguir mostra o aplicativo:

![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Selecione CTRL + F5 para executar o aplicativo sem depuração.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * Inicia o [Kestrel](xref:fundamentals/servers/kestrel)
  * Inicia um navegador.
  * Navega para `https://localhost:5001` .

  A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`. O nome do host padrão para seu computador local é `localhost` . Localhost serve somente solicitações da Web do computador local.

Iniciar o aplicativo sem depuração selecionando CTRL + F5 permite que você:

* Realize alterações de código.
* Salve o arquivo.
* Atualize rapidamente o navegador e veja as alterações no código.

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.

  Visual Studio para Mac: inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel) , inicia um navegador e navega para `http://localhost:port` , onde *porta* é um número de porta escolhido aleatoriamente.

[!INCLUDE[](~/includes/trustCertMac.md)]

A barra de endereços mostra `localhost:port#` e não algo como `example.com`. O nome do host padrão para seu computador local é `localhost` . Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web. Quando você executar o aplicativo, você verá um número da porta diferente.

Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.

A imagem a seguir mostra o aplicativo:

![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.

> [!div class="step-by-step"]
> [Próximo](adding-controller.md)

::: moniker-end
