---
title: Compilar um Blazor aplicativo de lista de tarefas pendentes
author: guardrex
description: Crie um Blazor aplicativo passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2021
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: d984023a1c46c5383d47a1634c54e61747b83d60
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101204"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="a1866-103">Compilar um Blazor aplicativo de lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a1866-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="a1866-104">Este tutorial mostra como criar e modificar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1866-104">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="a1866-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="a1866-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a1866-106">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="a1866-106">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a1866-107">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="a1866-107">Modify Razor components</span></span>
> * <span data-ttu-id="a1866-108">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="a1866-108">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a1866-109">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="a1866-109">Use routing in a Blazor app</span></span>

<span data-ttu-id="a1866-110">No final deste tutorial, você terá um aplicativo de lista de tarefas em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="a1866-110">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a1866-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="a1866-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="a1866-112">Criar um aplicativo de lista de tarefas pendentes Blazor</span><span class="sxs-lookup"><span data-stu-id="a1866-112">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="a1866-113">Crie um novo Blazor aplicativo chamado `TodoList` em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="a1866-113">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="a1866-114">O comando anterior cria uma pasta chamada `TodoList` com a `-o|--output` opção para manter o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1866-114">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="a1866-115">A `TodoList` pasta é a *pasta raiz* do projeto.</span><span class="sxs-lookup"><span data-stu-id="a1866-115">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="a1866-116">Altere os diretórios para a `TodoList` pasta com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a1866-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="a1866-117">Adicione um novo `Todo` Razor componente ao aplicativo usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a1866-117">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="a1866-118">A `-n|--name` opção no comando anterior especifica o nome do novo Razor componente.</span><span class="sxs-lookup"><span data-stu-id="a1866-118">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="a1866-119">O novo componente é criado na pasta do projeto `Pages` com a `-o|--output` opção.</span><span class="sxs-lookup"><span data-stu-id="a1866-119">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="a1866-120">Razor os nomes de arquivo de componente exigem uma primeira letra em maiúscula.</span><span class="sxs-lookup"><span data-stu-id="a1866-120">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="a1866-121">Abra a `Pages` pasta e confirme se o `Todo` nome do arquivo de componente começa com uma letra maiúscula `T` .</span><span class="sxs-lookup"><span data-stu-id="a1866-121">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="a1866-122">O nome do arquivo deve ser `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="a1866-122">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="a1866-123">Abra o `Todo` componente em qualquer editor de arquivo e adicione uma `@page` Razor diretiva à parte superior do arquivo com uma URL relativa de `/todo` .</span><span class="sxs-lookup"><span data-stu-id="a1866-123">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="a1866-124">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="a1866-124">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   <span data-ttu-id="a1866-125">Salve o arquivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a1866-125">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="a1866-126">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="a1866-126">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="a1866-127">O `NavMenu` componente é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1866-127">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="a1866-128">Layouts são componentes que permitem evitar a duplicação de conteúdo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1866-128">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="a1866-129">O `NavLink` componente fornece uma indicação na interface do usuário do aplicativo quando a URL do componente é carregada pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1866-129">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="a1866-130">Na lista não ordenada ( `<ul>...</ul>` ) do `NavMenu` componente, adicione o seguinte item de lista ( `<li>...</li>` ) e `NavLink` componente para o `Todo` componente.</span><span class="sxs-lookup"><span data-stu-id="a1866-130">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="a1866-131">Em `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="a1866-131">In `Shared/NavMenu.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   <span data-ttu-id="a1866-132">Salve o arquivo `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="a1866-132">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="a1866-133">Compile e execute o aplicativo executando o [`dotnet watch run`](xref:tutorials/dotnet-watch) comando no Shell de comando da `TodoList` pasta.</span><span class="sxs-lookup"><span data-stu-id="a1866-133">Build and run the app by executing the [`dotnet watch run`](xref:tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="a1866-134">Depois que o aplicativo estiver em execução, visite a nova página todo selecionando o **`Todo`** link na barra de navegação do aplicativo, que carrega a página em `/todo` .</span><span class="sxs-lookup"><span data-stu-id="a1866-134">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="a1866-135">Deixe o aplicativo executando o Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a1866-135">Leave the app running the command shell.</span></span> <span data-ttu-id="a1866-136">Cada vez que um arquivo é salvo, o aplicativo é recriado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="a1866-136">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="a1866-137">O navegador perde temporariamente sua conexão com o aplicativo durante a compilação e a reinicialização.</span><span class="sxs-lookup"><span data-stu-id="a1866-137">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="a1866-138">A página no navegador é recarregada automaticamente quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="a1866-138">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="a1866-139">Adicione um `TodoItem.cs` arquivo à raiz do projeto (a `TodoList` pasta) para manter uma classe que representa um item de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="a1866-139">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="a1866-140">Use o seguinte código de C# para a classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a1866-140">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="a1866-141">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="a1866-141">`TodoItem.cs`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > <span data-ttu-id="a1866-142">Se estiver usando o Visual Studio para criar o `ToDoItem.cs` arquivo e a `ToDoItem` classe, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="a1866-142">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="a1866-143">Remova o namespace que o Visual Studio gera para a classe.</span><span class="sxs-lookup"><span data-stu-id="a1866-143">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="a1866-144">Use o botão de **cópia** no bloco de código anterior e substitua todo o conteúdo do arquivo que o Visual Studio gera.</span><span class="sxs-lookup"><span data-stu-id="a1866-144">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="a1866-145">Retorne ao `Todo` componente e execute as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="a1866-145">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="a1866-146">Adicione um campo para os itens de tarefas no `@code` bloco.</span><span class="sxs-lookup"><span data-stu-id="a1866-146">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="a1866-147">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="a1866-147">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="a1866-148">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="a1866-148">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="a1866-149">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="a1866-149">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. <span data-ttu-id="a1866-150">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="a1866-150">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="a1866-151">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="a1866-151">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. <span data-ttu-id="a1866-152">Salve o `TodoItem.cs` arquivo e o `Pages/Todo.razor` arquivo atualizado.</span><span class="sxs-lookup"><span data-stu-id="a1866-152">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a1866-153">No Shell de comando, o aplicativo é recriado automaticamente quando os arquivos são salvos.</span><span class="sxs-lookup"><span data-stu-id="a1866-153">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="a1866-154">O navegador perde temporariamente sua conexão com o aplicativo e, em seguida, recarrega a página quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="a1866-154">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="a1866-155">Quando o **`Add todo`** botão é selecionado, nada acontece porque um manipulador de eventos não está anexado ao botão.</span><span class="sxs-lookup"><span data-stu-id="a1866-155">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="a1866-156">Adicione um `AddTodo` método ao `Todo` componente e registre o método para o botão usando o `@onclick` atributo.</span><span class="sxs-lookup"><span data-stu-id="a1866-156">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="a1866-157">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="a1866-157">The `AddTodo` C# method is called when the button is selected:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. <span data-ttu-id="a1866-158">Para obter o título do novo item de tarefas pendentes, adicione um `newTodo` campo de cadeia de caracteres na parte superior do `@code` bloco:</span><span class="sxs-lookup"><span data-stu-id="a1866-158">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   <span data-ttu-id="a1866-159">Modifique o `<input>` elemento Text para associar ao `newTodo` `@bind` atributo:</span><span class="sxs-lookup"><span data-stu-id="a1866-159">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="a1866-160">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="a1866-160">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="a1866-161">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="a1866-161">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. <span data-ttu-id="a1866-162">Salve o arquivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a1866-162">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a1866-163">O aplicativo é recriado automaticamente no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a1866-163">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a1866-164">A página é recarregada no navegador depois que o navegador se reconecta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1866-164">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a1866-165">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="a1866-165">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="a1866-166">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="a1866-166">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="a1866-167">Alterar `@todo.Title` para um `<input>` elemento associado a `todo.Title` `@bind` :</span><span class="sxs-lookup"><span data-stu-id="a1866-167">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. <span data-ttu-id="a1866-168">Atualize o `<h3>` cabeçalho para mostrar uma contagem do número de itens de tarefas pendentes que não estão concluídos ( `IsDone` is `false` ).</span><span class="sxs-lookup"><span data-stu-id="a1866-168">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="a1866-169">O `Todo` componente concluído ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="a1866-169">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. <span data-ttu-id="a1866-170">Salve o arquivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a1866-170">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a1866-171">O aplicativo é recriado automaticamente no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a1866-171">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a1866-172">A página é recarregada no navegador depois que o navegador se reconecta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1866-172">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a1866-173">Adicione itens, edite itens e marque itens de tarefas pendentes para testar o componente.</span><span class="sxs-lookup"><span data-stu-id="a1866-173">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="a1866-174">Quando terminar, desligue o aplicativo no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a1866-174">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="a1866-175">Muitos shells de comando aceitam o comando de teclado <kbd>Ctrl</kbd> + <kbd>c</kbd> para interromper um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1866-175">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a1866-176">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a1866-176">Next steps</span></span>

<span data-ttu-id="a1866-177">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="a1866-177">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a1866-178">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="a1866-178">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a1866-179">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="a1866-179">Modify Razor components</span></span>
> * <span data-ttu-id="a1866-180">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="a1866-180">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a1866-181">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="a1866-181">Use routing in a Blazor app</span></span>

<span data-ttu-id="a1866-182">Saiba mais sobre as ferramentas para ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="a1866-182">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
