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

<span data-ttu-id="b31d0-101">Execute o Identity scaffolder:</span><span class="sxs-lookup"><span data-stu-id="b31d0-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b31d0-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b31d0-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b31d0-103">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b31d0-104">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **Identity** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="b31d0-105">Na caixa de diálogo **Adicionar Identity** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="b31d0-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="b31d0-106">Selecione a página de layout existente para que o arquivo de layout não seja substituído por marcação incorreta.</span><span class="sxs-lookup"><span data-stu-id="b31d0-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="b31d0-107">Quando um arquivo *\_ layout. cshtml* existente é selecionado, ele **não** é substituído.</span><span class="sxs-lookup"><span data-stu-id="b31d0-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="b31d0-108">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b31d0-108">For example:</span></span>
    * <span data-ttu-id="b31d0-109">`~/Pages/Shared/_Layout.cshtml` para Razor páginas ou Blazor Server projetos com Razor infraestrutura de páginas existentes</span><span class="sxs-lookup"><span data-stu-id="b31d0-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="b31d0-110">`~/Views/Shared/_Layout.cshtml` para projetos ou Blazor Server projetos MVC com infraestrutura MVC existente</span><span class="sxs-lookup"><span data-stu-id="b31d0-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="b31d0-111">Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="b31d0-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="b31d0-112">Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="b31d0-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="b31d0-113">Selecione sua classe de contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="b31d0-113">Select your data context class.</span></span>
  * <span data-ttu-id="b31d0-114">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-114">Select **Add**.</span></span>
* <span data-ttu-id="b31d0-115">Para criar um novo contexto de usuário e, possivelmente, criar uma classe de usuário personalizada para Identity :</span><span class="sxs-lookup"><span data-stu-id="b31d0-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="b31d0-116">Selecione o **+** botão para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="b31d0-117">Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="b31d0-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="b31d0-118">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-118">Select **Add**.</span></span>

<span data-ttu-id="b31d0-119">Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="b31d0-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b31d0-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b31d0-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b31d0-121">Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="b31d0-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="b31d0-122">Adicione as referências de pacote NuGet necessárias ao arquivo de projeto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="b31d0-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="b31d0-123">Execute os seguintes comandos no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="b31d0-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="b31d0-124">Execute o seguinte comando para listar as Identity Opções de scaffolder:</span><span class="sxs-lookup"><span data-stu-id="b31d0-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="b31d0-125">Na pasta do projeto, execute o Identity scaffolder com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="b31d0-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="b31d0-126">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="b31d0-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="b31d0-127">Use o nome totalmente qualificado correto para seu contexto de BD:</span><span class="sxs-lookup"><span data-stu-id="b31d0-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="b31d0-128">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="b31d0-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="b31d0-129">Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas.</span><span class="sxs-lookup"><span data-stu-id="b31d0-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="b31d0-130">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b31d0-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="b31d0-131">Se você executar o Identity scaffolder sem especificar o `--files` sinalizador ou o `--useDefaultUI` sinalizador, todas as Identity páginas da interface do usuário disponíveis serão criadas em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b31d0-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b31d0-132">Execute o Identity scaffolder:</span><span class="sxs-lookup"><span data-stu-id="b31d0-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b31d0-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b31d0-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b31d0-134">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b31d0-135">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **Identity** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="b31d0-136">Na caixa de diálogo **Adicionar Identity** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="b31d0-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="b31d0-137">Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta.</span><span class="sxs-lookup"><span data-stu-id="b31d0-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="b31d0-138">Quando um arquivo *\_ layout. cshtml* existente é selecionado, ele **não** é substituído.</span><span class="sxs-lookup"><span data-stu-id="b31d0-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="b31d0-139">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b31d0-139">For example:</span></span>
    * <span data-ttu-id="b31d0-140">`~/Pages/Shared/_Layout.cshtml` para Razor páginas</span><span class="sxs-lookup"><span data-stu-id="b31d0-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="b31d0-141">`~/Views/Shared/_Layout.cshtml` para projetos do MVC</span><span class="sxs-lookup"><span data-stu-id="b31d0-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="b31d0-142">Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="b31d0-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="b31d0-143">Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="b31d0-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="b31d0-144">Selecione sua classe de contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="b31d0-144">Select your data context class.</span></span>
  * <span data-ttu-id="b31d0-145">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-145">Select **Add**.</span></span>
* <span data-ttu-id="b31d0-146">Para criar um novo contexto de usuário e, possivelmente, criar uma classe de usuário personalizada para Identity :</span><span class="sxs-lookup"><span data-stu-id="b31d0-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="b31d0-147">Selecione o **+** botão para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="b31d0-148">Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="b31d0-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="b31d0-149">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b31d0-149">Select **Add**.</span></span>

<span data-ttu-id="b31d0-150">Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="b31d0-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b31d0-151">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b31d0-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b31d0-152">Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="b31d0-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="b31d0-153">Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="b31d0-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="b31d0-154">Execute os seguintes comandos no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="b31d0-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="b31d0-155">Execute o seguinte comando para listar as Identity Opções de scaffolder:</span><span class="sxs-lookup"><span data-stu-id="b31d0-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="b31d0-156">Na pasta do projeto, execute o Identity scaffolder com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="b31d0-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="b31d0-157">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="b31d0-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="b31d0-158">Use o nome totalmente qualificado correto para seu contexto de BD:</span><span class="sxs-lookup"><span data-stu-id="b31d0-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="b31d0-159">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="b31d0-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="b31d0-160">Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas.</span><span class="sxs-lookup"><span data-stu-id="b31d0-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="b31d0-161">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b31d0-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="b31d0-162">Se você executar o Identity scaffolder sem especificar o `--files` sinalizador ou o `--useDefaultUI` sinalizador, todas as Identity páginas da interface do usuário disponíveis serão criadas em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b31d0-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
