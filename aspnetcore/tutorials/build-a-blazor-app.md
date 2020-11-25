---
title: Compilar um Blazor aplicativo de lista de tarefas pendentes
author: guardrex
description: Crie um Blazor aplicativo passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870432"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="a91f2-103">Compilar um Blazor aplicativo de lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a91f2-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="a91f2-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a91f2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a91f2-105">Este tutorial mostra como criar e modificar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a91f2-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="a91f2-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="a91f2-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a91f2-107">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="a91f2-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a91f2-108">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="a91f2-108">Modify Razor components</span></span>
> * <span data-ttu-id="a91f2-109">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="a91f2-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a91f2-110">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="a91f2-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="a91f2-111">No final deste tutorial, você terá um aplicativo de lista de tarefas em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="a91f2-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a91f2-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="a91f2-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="a91f2-113">Criar um aplicativo de lista de tarefas pendentes Blazor</span><span class="sxs-lookup"><span data-stu-id="a91f2-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="a91f2-114">Crie um novo Blazor aplicativo chamado `TodoList` em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="a91f2-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="a91f2-115">O comando anterior cria uma pasta chamada `TodoList` para manter o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a91f2-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="a91f2-116">A `TodoList` pasta é a *pasta raiz* do projeto.</span><span class="sxs-lookup"><span data-stu-id="a91f2-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="a91f2-117">Altere os diretórios para a `TodoList` pasta com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a91f2-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="a91f2-118">Adicione um novo `Todo` Razor componente ao aplicativo na `Pages` pasta usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a91f2-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="a91f2-119">Razor os nomes de arquivo de componente exigem uma primeira letra em maiúscula.</span><span class="sxs-lookup"><span data-stu-id="a91f2-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="a91f2-120">Abra a `Pages` pasta e confirme se o `Todo` nome do arquivo de componente começa com uma letra maiúscula `T` .</span><span class="sxs-lookup"><span data-stu-id="a91f2-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="a91f2-121">O nome do arquivo deve ser `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="a91f2-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="a91f2-122">No, `Pages/Todo.razor` forneça a marcação inicial para o componente:</span><span class="sxs-lookup"><span data-stu-id="a91f2-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="a91f2-123">Salve o arquivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a91f2-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="a91f2-124">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="a91f2-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="a91f2-125">O `NavMenu` componente ( `Shared/NavMenu.razor` ) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a91f2-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="a91f2-126">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a91f2-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="a91f2-127">Adicione um `<NavLink>` elemento para o `Todo` componente adicionando a seguinte marcação de item de lista abaixo dos itens de lista existentes no `Shared/NavMenu.razor` arquivo:</span><span class="sxs-lookup"><span data-stu-id="a91f2-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="a91f2-128">Salve o arquivo `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="a91f2-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="a91f2-129">Compile e execute o aplicativo executando o [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) comando no Shell de comando da `TodoList` pasta.</span><span class="sxs-lookup"><span data-stu-id="a91f2-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="a91f2-130">Visite a nova página todo em `https://localhost:5001/todo` para confirmar que o link de navegação da barra lateral para o `Todo` componente funciona.</span><span class="sxs-lookup"><span data-stu-id="a91f2-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="a91f2-131">Adicione um `TodoItem.cs` arquivo à raiz do projeto (a `TodoList` pasta) para manter uma classe que representa um item de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="a91f2-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="a91f2-132">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="a91f2-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="a91f2-133">Retornar ao `Todo` componente ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="a91f2-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="a91f2-134">Adicione um campo para os itens de tarefas pendentes em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="a91f2-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="a91f2-135">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="a91f2-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="a91f2-136">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="a91f2-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="a91f2-137">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="a91f2-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="a91f2-138">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="a91f2-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="a91f2-139">Salve o `TodoItem.cs` arquivo e o `Pages/Todo.razor` arquivo atualizado.</span><span class="sxs-lookup"><span data-stu-id="a91f2-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a91f2-140">No Shell de comando, o aplicativo é recriado automaticamente quando os arquivos são salvos.</span><span class="sxs-lookup"><span data-stu-id="a91f2-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="a91f2-141">O navegador perde temporariamente sua conexão com o aplicativo e, em seguida, recarrega a página quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="a91f2-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="a91f2-142">Quando o **`Add todo`** botão é selecionado, nada acontece porque um manipulador de eventos não está anexado ao botão.</span><span class="sxs-lookup"><span data-stu-id="a91f2-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="a91f2-143">Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="a91f2-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="a91f2-144">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="a91f2-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="a91f2-145">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="a91f2-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="a91f2-146">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="a91f2-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="a91f2-147">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="a91f2-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="a91f2-148">Salve o arquivo `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a91f2-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="a91f2-149">O aplicativo é recriado automaticamente no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a91f2-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a91f2-150">A página é recarregada no navegador depois que o navegador se reconecta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a91f2-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a91f2-151">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="a91f2-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="a91f2-152">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="a91f2-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="a91f2-153">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="a91f2-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="a91f2-154">Para verificar se esses valores estão associados, atualize o cabeçalho `<h3>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="a91f2-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="a91f2-155">O `Todo` componente concluído ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="a91f2-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="a91f2-156">Salve o arquivo `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a91f2-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="a91f2-157">O aplicativo é recriado automaticamente no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a91f2-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a91f2-158">A página é recarregada no navegador depois que o navegador se reconecta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a91f2-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a91f2-159">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="a91f2-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="a91f2-160">Quando terminar, desligue o aplicativo no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a91f2-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="a91f2-161">Muitos shells de comando aceitam o comando de teclado <kbd>Ctrl</kbd> + <kbd>c</kbd> para interromper um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a91f2-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a91f2-162">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a91f2-162">Next steps</span></span>

<span data-ttu-id="a91f2-163">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="a91f2-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a91f2-164">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="a91f2-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a91f2-165">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="a91f2-165">Modify Razor components</span></span>
> * <span data-ttu-id="a91f2-166">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="a91f2-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a91f2-167">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="a91f2-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="a91f2-168">Saiba mais sobre as ferramentas para ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="a91f2-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
