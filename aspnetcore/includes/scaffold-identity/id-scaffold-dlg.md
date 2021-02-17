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
<span data-ttu-id="f22dc-101">Execute o Identity scaffolder:</span><span class="sxs-lookup"><span data-stu-id="f22dc-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f22dc-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f22dc-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f22dc-103">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar**  >  **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="f22dc-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f22dc-104">No painel esquerdo da caixa de diálogo **Adicionar novo item do com Scaffold** , selecione **Identity**  >  **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f22dc-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="f22dc-105">Na caixa de diálogo **Adicionar Identity** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="f22dc-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="f22dc-106">Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta:</span><span class="sxs-lookup"><span data-stu-id="f22dc-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="f22dc-107">`~/Pages/Shared/_Layout.cshtml` para Razor páginas</span><span class="sxs-lookup"><span data-stu-id="f22dc-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="f22dc-108">`~/Views/Shared/_Layout.cshtml` para projetos do MVC</span><span class="sxs-lookup"><span data-stu-id="f22dc-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="f22dc-109">Blazor Server os aplicativos criados a partir do Blazor Server modelo ( `blazorserver` ) não são configurados para Razor páginas ou MVC por padrão.</span><span class="sxs-lookup"><span data-stu-id="f22dc-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="f22dc-110">Deixe a entrada da página de layout em branco.</span><span class="sxs-lookup"><span data-stu-id="f22dc-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="f22dc-111">Selecione o **+** botão para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="f22dc-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="f22dc-112">Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="f22dc-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="f22dc-113">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f22dc-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f22dc-114">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f22dc-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f22dc-115">Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="f22dc-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="f22dc-116">Adicione as referências de pacote NuGet necessárias ao arquivo de projeto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="f22dc-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="f22dc-117">Execute os seguintes comandos no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="f22dc-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="f22dc-118">Execute o seguinte comando para listar as Identity Opções de scaffolder:</span><span class="sxs-lookup"><span data-stu-id="f22dc-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="f22dc-119">Na pasta do projeto, execute o Identity scaffolder com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="f22dc-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="f22dc-120">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f22dc-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
