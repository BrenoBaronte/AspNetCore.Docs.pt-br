---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: d4eb1744b1186704603430584b3da0793f90ee49
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213087"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Introdução ao ASP.NET Core MVC

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.

O aplicativo gerencia um banco de dados de títulos de filmes. Você aprenderá como:

> [!div class="checklist"]
> * Crie um aplicativo Web.
> * Adicionar e gerar o scaffolding de um modelo.
> * Trabalhar com um banco de dados.
> * Adicionar pesquisa e validação.

No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

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

* No Visual Studio, selecione **Criar um projeto**.

* Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**. É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.

![Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core ](start-mvc/_static/new_project30.png)

O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar. Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções. Este é um projeto inicial básico.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

O tutorial pressupõe que você já tenha familiaridade com o VS Code. Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.
* Execute o seguinte comando:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**  Selecione **Sim**.

  * `dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.
  * `code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**. Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* Na caixa de diálogo **configurar seu novo aplicativo Web** :

  * Confirme se a **autenticação** está definida como **sem autenticação**.
  * Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão 3. x mais recente.

  Selecione **Avançar**.

* Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Executar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.

[!INCLUDE[](~/includes/trustCertVS.md)]

* O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.
* Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.
* Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* Você pode depurar o aplicativo selecionando o botão **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

  A imagem a seguir mostra o aplicativo:

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pressione Ctrl + F5 para execução sem o depurador.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`. A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

  Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo. O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.

[!INCLUDE[](~/includes/trustCertMac.md)]

* A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web. Quando você executar o aplicativo, você verá um número da porta diferente.
* Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.

  A imagem a seguir mostra o aplicativo:

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.

> [!div class="step-by-step"]
> [Avançar](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.

O aplicativo gerencia um banco de dados de títulos de filmes. Você aprenderá como:

> [!div class="checklist"]
> * Crie um aplicativo Web.
> * Adicionar e gerar o scaffolding de um modelo.
> * Trabalhar com um banco de dados.
> * Adicionar pesquisa e validação.

No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Criar um aplicativo Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No Visual Studio, selecione **Criar um projeto**.

* Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**. É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.

![Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core ](start-mvc/_static/new_project22-21.png)

O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar. Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções. Este é um projeto inicial básico e é um bom ponto de partida.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

O tutorial pressupõe que você já tenha familiaridade com o VS Code. Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.
* Execute o seguinte comando:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**  Selecione **Sim**.

  * `dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.
  * `code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**. Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.

* Na caixa de diálogo **configurar seu novo aplicativo Web** :

  * Confirme se a **autenticação** está definida como **sem autenticação**.
  * Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão 2. x mais recente.

  Selecione **Avançar**.

* Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.

---

### <a name="run-the-app"></a>Executar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.

[!INCLUDE[](~/includes/trustCertVS.md)]

* O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.
* Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.
* Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* Você pode depurar o aplicativo selecionando o botão **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

* Selecione **Aceitar** para consentir com o acompanhamento. Este aplicativo não acompanha informações pessoais. O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pressione Ctrl + F5 para execução sem o depurador.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`. A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

  Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.

* Selecione **Aceitar** para consentir com o acompanhamento. Este aplicativo não acompanha informações pessoais. O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo. O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.

[!INCLUDE[](~/includes/trustCertMac.md)]

* A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web. Quando você executar o aplicativo, você verá um número da porta diferente.
* Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.

* Selecione **Aceitar** para consentir com o acompanhamento. Este aplicativo não acompanha informações pessoais. O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.

> [!div class="step-by-step"]
> [Avançar](adding-controller.md)

::: moniker-end
