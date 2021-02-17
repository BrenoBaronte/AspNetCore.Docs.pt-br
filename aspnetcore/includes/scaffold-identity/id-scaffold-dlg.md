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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552026"
---
Execute o Identity scaffolder:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar**  >  **novo item com Scaffold**.
* No painel esquerdo da caixa de diálogo **Adicionar novo item do com Scaffold** , selecione **Identity**  >  **Adicionar**.
* Na caixa de diálogo **Adicionar Identity** , selecione as opções desejadas.
  * Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta:
    * `~/Pages/Shared/_Layout.cshtml` para Razor páginas
    * `~/Views/Shared/_Layout.cshtml` para projetos do MVC
    * Blazor Server os aplicativos criados a partir do Blazor Server modelo ( `blazorserver` ) não são configurados para Razor páginas ou MVC por padrão. Deixe a entrada da página de layout em branco.
  * Selecione o **+** botão para criar uma nova **classe de contexto de dados**. Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).
* Selecione **Adicionar**.

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

Na pasta do projeto, execute o Identity scaffolder com as opções desejadas. Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o seguinte comando:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
