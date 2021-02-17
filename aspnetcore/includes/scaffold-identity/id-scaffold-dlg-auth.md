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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552194"
---
::: moniker range=">= aspnetcore-3.0"

Execute o Identity scaffolder:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.
* No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **Identity** > **Adicionar**.
* Na caixa de diálogo **Adicionar Identity** , selecione as opções desejadas.
  * Selecione a página de layout existente para que o arquivo de layout não seja substituído por marcação incorreta. Quando um arquivo *\_ layout. cshtml* existente é selecionado, ele **não** é substituído. Por exemplo:
    * `~/Pages/Shared/_Layout.cshtml` para Razor páginas ou Blazor Server projetos com Razor infraestrutura de páginas existentes
    * `~/Views/Shared/_Layout.cshtml` para projetos ou Blazor Server projetos MVC com infraestrutura MVC existente
* Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir. Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.
  * Selecione sua classe de contexto de dados.
  * Selecione **Adicionar**.
* Para criar um novo contexto de usuário e, possivelmente, criar uma classe de usuário personalizada para Identity :
  * Selecione o **+** botão para criar uma nova **classe de contexto de dados**. Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).
  * Selecione **Adicionar**.

Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione as referências de pacote NuGet necessárias ao arquivo de projeto (*. csproj*). Execute os seguintes comandos no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Execute o seguinte comando para listar as Identity Opções de scaffolder:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Na pasta do projeto, execute o Identity scaffolder com as opções desejadas. Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir. Use o nome totalmente qualificado correto para seu contexto de BD:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas. Por exemplo:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Se você executar o Identity scaffolder sem especificar o `--files` sinalizador ou o `--useDefaultUI` sinalizador, todas as Identity páginas da interface do usuário disponíveis serão criadas em seu projeto.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Execute o Identity scaffolder:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.
* No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **Identity** > **Adicionar**.
* Na caixa de diálogo **Adicionar Identity** , selecione as opções desejadas.
  * Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta. Quando um arquivo *\_ layout. cshtml* existente é selecionado, ele **não** é substituído. Por exemplo:
    * `~/Pages/Shared/_Layout.cshtml` para Razor páginas
    * `~/Views/Shared/_Layout.cshtml` para projetos do MVC
* Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir. Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.
  * Selecione sua classe de contexto de dados.
  * Selecione **Adicionar**.
* Para criar um novo contexto de usuário e, possivelmente, criar uma classe de usuário personalizada para Identity :
  * Selecione o **+** botão para criar uma nova **classe de contexto de dados**. Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).
  * Selecione **Adicionar**.

Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (*. csproj*). Execute os seguintes comandos no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as Identity Opções de scaffolder:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute o Identity scaffolder com as opções desejadas. Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir. Use o nome totalmente qualificado correto para seu contexto de BD:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas. Por exemplo:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Se você executar o Identity scaffolder sem especificar o `--files` sinalizador ou o `--useDefaultUI` sinalizador, todas as Identity páginas da interface do usuário disponíveis serão criadas em seu projeto.

---

::: moniker-end
