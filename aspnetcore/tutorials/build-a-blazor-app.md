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
# <a name="build-a-blazor-todo-list-app"></a>Compilar um Blazor aplicativo de lista de tarefas pendentes

Este tutorial mostra como criar e modificar um Blazor aplicativo. Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo de lista de tarefas Blazor
> * Modificar Razor componentes
> * Usar manipulação de eventos e vinculação de dados em componentes
> * Usar o roteamento em um Blazor aplicativo

No final deste tutorial, você terá um aplicativo de lista de tarefas em funcionamento.

## <a name="prerequisites"></a>Pré-requisitos

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a>Criar um aplicativo de lista de tarefas pendentes Blazor

1. Crie um novo Blazor aplicativo chamado `TodoList` em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   O comando anterior cria uma pasta chamada `TodoList` com a `-o|--output` opção para manter o aplicativo. A `TodoList` pasta é a *pasta raiz* do projeto. Altere os diretórios para a `TodoList` pasta com o seguinte comando:

   ```dotnetcli
   cd TodoList
   ```

1. Adicione um novo `Todo` Razor componente ao aplicativo usando o seguinte comando:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   A `-n|--name` opção no comando anterior especifica o nome do novo Razor componente. O novo componente é criado na pasta do projeto `Pages` com a `-o|--output` opção.

   > [!IMPORTANT]
   > Razor os nomes de arquivo de componente exigem uma primeira letra em maiúscula. Abra a `Pages` pasta e confirme se o `Todo` nome do arquivo de componente começa com uma letra maiúscula `T` . O nome do arquivo deve ser `Todo.razor` .

1. Abra o `Todo` componente em qualquer editor de arquivo e adicione uma `@page` Razor diretiva à parte superior do arquivo com uma URL relativa de `/todo` .

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   Salve o arquivo `Pages/Todo.razor`.

1. Adicione o componente `Todo` à barra de navegação.

   O `NavMenu` componente é usado no layout do aplicativo. Layouts são componentes que permitem evitar a duplicação de conteúdo em um aplicativo. O `NavLink` componente fornece uma indicação na interface do usuário do aplicativo quando a URL do componente é carregada pelo aplicativo.

   Na lista não ordenada ( `<ul>...</ul>` ) do `NavMenu` componente, adicione o seguinte item de lista ( `<li>...</li>` ) e `NavLink` componente para o `Todo` componente.

   Em `Shared/NavMenu.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   Salve o arquivo `Shared/NavMenu.razor`.

1. Compile e execute o aplicativo executando o [`dotnet watch run`](xref:tutorials/dotnet-watch) comando no Shell de comando da `TodoList` pasta. Depois que o aplicativo estiver em execução, visite a nova página todo selecionando o **`Todo`** link na barra de navegação do aplicativo, que carrega a página em `/todo` .

   Deixe o aplicativo executando o Shell de comando. Cada vez que um arquivo é salvo, o aplicativo é recriado automaticamente. O navegador perde temporariamente sua conexão com o aplicativo durante a compilação e a reinicialização. A página no navegador é recarregada automaticamente quando a conexão é restabelecida.

1. Adicione um `TodoItem.cs` arquivo à raiz do projeto (a `TodoList` pasta) para manter uma classe que representa um item de tarefas pendentes. Use o seguinte código de C# para a classe `TodoItem`.

   `TodoItem.cs`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > Se estiver usando o Visual Studio para criar o `ToDoItem.cs` arquivo e a `ToDoItem` classe, use uma das seguintes abordagens:
   >
   > * Remova o namespace que o Visual Studio gera para a classe.
   > * Use o botão de **cópia** no bloco de código anterior e substitua todo o conteúdo do arquivo que o Visual Studio gera.

1. Retorne ao `Todo` componente e execute as seguintes tarefas:

   * Adicione um campo para os itens de tarefas no `@code` bloco. O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.
   * Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista. Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. Salve o `TodoItem.cs` arquivo e o `Pages/Todo.razor` arquivo atualizado. No Shell de comando, o aplicativo é recriado automaticamente quando os arquivos são salvos. O navegador perde temporariamente sua conexão com o aplicativo e, em seguida, recarrega a página quando a conexão é restabelecida.

1. Quando o **`Add todo`** botão é selecionado, nada acontece porque um manipulador de eventos não está anexado ao botão.

1. Adicione um `AddTodo` método ao `Todo` componente e registre o método para o botão usando o `@onclick` atributo. O método C# `AddTodo` é chamado quando o botão é selecionado:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. Para obter o título do novo item de tarefas pendentes, adicione um `newTodo` campo de cadeia de caracteres na parte superior do `@code` bloco:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   Modifique o `<input>` elemento Text para associar ao `newTodo` `@bind` atributo:

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista. Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. Salve o arquivo `Pages/Todo.razor`. O aplicativo é recriado automaticamente no Shell de comando. A página é recarregada no navegador depois que o navegador se reconecta ao aplicativo.

1. O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos. Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`. Alterar `@todo.Title` para um `<input>` elemento associado a `todo.Title` `@bind` :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. Atualize o `<h3>` cabeçalho para mostrar uma contagem do número de itens de tarefas pendentes que não estão concluídos ( `IsDone` is `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. O `Todo` componente concluído ( `Pages/Todo.razor` ):

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. Salve o arquivo `Pages/Todo.razor`. O aplicativo é recriado automaticamente no Shell de comando. A página é recarregada no navegador depois que o navegador se reconecta ao aplicativo.

1. Adicione itens, edite itens e marque itens de tarefas pendentes para testar o componente.

1. Quando terminar, desligue o aplicativo no Shell de comando. Muitos shells de comando aceitam o comando de teclado <kbd>Ctrl</kbd> + <kbd>c</kbd> para interromper um aplicativo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um projeto de aplicativo de lista de tarefas Blazor
> * Modificar Razor componentes
> * Usar manipulação de eventos e vinculação de dados em componentes
> * Usar o roteamento em um Blazor aplicativo

Saiba mais sobre as ferramentas para ASP.NET Core Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
