---
title: Compilar um Blazor aplicativo de lista de tarefas pendentes
author: guardrex
description: Crie um Blazor aplicativo passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
ms.openlocfilehash: 87626ff30589de82a04c95634fc0dcbcf2eeac18
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507001"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="bdb74-103">Compilar um Blazor aplicativo de lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="bdb74-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="bdb74-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bdb74-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bdb74-105">Este tutorial mostra como criar e modificar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="bdb74-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="bdb74-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bdb74-107">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="bdb74-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="bdb74-108">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="bdb74-108">Modify Razor components</span></span>
> * <span data-ttu-id="bdb74-109">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="bdb74-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="bdb74-110">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="bdb74-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="bdb74-111">No final deste tutorial, você terá um aplicativo de lista de tarefas em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="bdb74-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bdb74-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="bdb74-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="bdb74-113">Criar um aplicativo de lista de tarefas pendentes Blazor</span><span class="sxs-lookup"><span data-stu-id="bdb74-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="bdb74-114">Crie um novo Blazor aplicativo chamado `TodoList` em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="bdb74-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="bdb74-115">O comando anterior cria uma pasta chamada `TodoList` com a `-o|--output` opção para manter o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="bdb74-116">A `TodoList` pasta é a *pasta raiz* do projeto.</span><span class="sxs-lookup"><span data-stu-id="bdb74-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="bdb74-117">Altere os diretórios para a `TodoList` pasta com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="bdb74-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="bdb74-118">Adicione um novo `Todo` Razor componente ao aplicativo usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="bdb74-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="bdb74-119">A `-n|--name` opção no comando anterior especifica o nome do novo Razor componente.</span><span class="sxs-lookup"><span data-stu-id="bdb74-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="bdb74-120">O novo componente é criado na pasta do projeto `Pages` com a `-o|--output` opção.</span><span class="sxs-lookup"><span data-stu-id="bdb74-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="bdb74-121">Razor os nomes de arquivo de componente exigem uma primeira letra em maiúscula.</span><span class="sxs-lookup"><span data-stu-id="bdb74-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="bdb74-122">Abra a `Pages` pasta e confirme se o `Todo` nome do arquivo de componente começa com uma letra maiúscula `T` .</span><span class="sxs-lookup"><span data-stu-id="bdb74-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="bdb74-123">O nome do arquivo deve ser `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="bdb74-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="bdb74-124">Abra o `Todo` componente em qualquer editor de arquivo e adicione uma `@page` Razor diretiva à parte superior do arquivo com uma URL relativa de `/todo` .</span><span class="sxs-lookup"><span data-stu-id="bdb74-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="bdb74-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="bdb74-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo0.razor?highlight=1)]

   <span data-ttu-id="bdb74-126">Salve o arquivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bdb74-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="bdb74-127">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdb74-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="bdb74-128">O `NavMenu` componente é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="bdb74-129">Layouts são componentes que permitem evitar a duplicação de conteúdo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="bdb74-130">O `NavLink` componente fornece uma indicação na interface do usuário do aplicativo quando a URL do componente é carregada pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="bdb74-131">Na lista não ordenada ( `<ul>...</ul>` ) do `NavMenu` componente, adicione o seguinte item de lista ( `<li>...</li>` ) e `NavLink` componente para o `Todo` componente.</span><span class="sxs-lookup"><span data-stu-id="bdb74-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="bdb74-132">Em `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="bdb74-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="bdb74-133">Salve o arquivo `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="bdb74-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="bdb74-134">Compile e execute o aplicativo executando o [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) comando no Shell de comando da `TodoList` pasta.</span><span class="sxs-lookup"><span data-stu-id="bdb74-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="bdb74-135">Depois que o aplicativo estiver em execução, visite a nova página todo selecionando o **`Todo`** link na barra de navegação do aplicativo, que carrega a página em `/todo` .</span><span class="sxs-lookup"><span data-stu-id="bdb74-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="bdb74-136">Deixe o aplicativo executando o Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="bdb74-136">Leave the app running the command shell.</span></span> <span data-ttu-id="bdb74-137">Cada vez que um arquivo é salvo, o aplicativo é recriado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="bdb74-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="bdb74-138">O navegador perde temporariamente sua conexão com o aplicativo durante a compilação e a reinicialização.</span><span class="sxs-lookup"><span data-stu-id="bdb74-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="bdb74-139">A página no navegador é recarregada automaticamente quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="bdb74-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="bdb74-140">Adicione um `TodoItem.cs` arquivo à raiz do projeto (a `TodoList` pasta) para manter uma classe que representa um item de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="bdb74-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="bdb74-141">Use o seguinte código de C# para a classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="bdb74-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="bdb74-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="bdb74-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="bdb74-143">Retorne ao `Todo` componente e execute as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="bdb74-143">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="bdb74-144">Adicione um campo para os itens de tarefas no `@code` bloco.</span><span class="sxs-lookup"><span data-stu-id="bdb74-144">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="bdb74-145">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="bdb74-145">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="bdb74-146">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="bdb74-146">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="bdb74-147">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="bdb74-147">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="bdb74-148">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="bdb74-148">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="bdb74-149">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="bdb74-149">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="bdb74-150">Salve o `TodoItem.cs` arquivo e o `Pages/Todo.razor` arquivo atualizado.</span><span class="sxs-lookup"><span data-stu-id="bdb74-150">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="bdb74-151">No Shell de comando, o aplicativo é recriado automaticamente quando os arquivos são salvos.</span><span class="sxs-lookup"><span data-stu-id="bdb74-151">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="bdb74-152">O navegador perde temporariamente sua conexão com o aplicativo e, em seguida, recarrega a página quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="bdb74-152">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="bdb74-153">Quando o **`Add todo`** botão é selecionado, nada acontece porque um manipulador de eventos não está anexado ao botão.</span><span class="sxs-lookup"><span data-stu-id="bdb74-153">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="bdb74-154">Adicione um `AddTodo` método ao `Todo` componente e registre o método para o botão usando o `@onclick` atributo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-154">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="bdb74-155">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="bdb74-155">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="bdb74-156">Para obter o título do novo item de tarefas pendentes, adicione um `newTodo` campo de cadeia de caracteres na parte superior do `@code` bloco:</span><span class="sxs-lookup"><span data-stu-id="bdb74-156">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=3)]

   <span data-ttu-id="bdb74-157">Modifique o `<input>` elemento Text para associar ao `newTodo` `@bind` atributo:</span><span class="sxs-lookup"><span data-stu-id="bdb74-157">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="bdb74-158">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="bdb74-158">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="bdb74-159">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="bdb74-159">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="bdb74-160">Salve o arquivo `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bdb74-160">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="bdb74-161">O aplicativo é recriado automaticamente no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="bdb74-161">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="bdb74-162">A página é recarregada no navegador depois que o navegador se reconecta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-162">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="bdb74-163">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="bdb74-163">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="bdb74-164">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="bdb74-164">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="bdb74-165">Alterar `@todo.Title` para um `<input>` elemento associado a `todo.Title` `@bind` :</span><span class="sxs-lookup"><span data-stu-id="bdb74-165">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=4-7)]

1. <span data-ttu-id="bdb74-166">Atualize o `<h3>` cabeçalho para mostrar uma contagem do número de itens de tarefas pendentes que não estão concluídos ( `IsDone` is `false` ).</span><span class="sxs-lookup"><span data-stu-id="bdb74-166">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="bdb74-167">O `Todo` componente concluído ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="bdb74-167">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="bdb74-168">Salve o arquivo `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bdb74-168">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="bdb74-169">O aplicativo é recriado automaticamente no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="bdb74-169">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="bdb74-170">A página é recarregada no navegador depois que o navegador se reconecta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-170">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="bdb74-171">Adicione itens, edite itens e marque itens de tarefas pendentes para testar o componente.</span><span class="sxs-lookup"><span data-stu-id="bdb74-171">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="bdb74-172">Quando terminar, desligue o aplicativo no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="bdb74-172">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="bdb74-173">Muitos shells de comando aceitam o comando de teclado <kbd>Ctrl</kbd> + <kbd>c</kbd> para interromper um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdb74-173">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bdb74-174">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="bdb74-174">Next steps</span></span>

<span data-ttu-id="bdb74-175">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="bdb74-175">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bdb74-176">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="bdb74-176">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="bdb74-177">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="bdb74-177">Modify Razor components</span></span>
> * <span data-ttu-id="bdb74-178">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="bdb74-178">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="bdb74-179">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="bdb74-179">Use routing in a Blazor app</span></span>

<span data-ttu-id="bdb74-180">Saiba mais sobre as ferramentas para ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="bdb74-180">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
