---
title: Criar serviços de back-end para aplicativos móveis nativos com o ASP.NET Core
author: rick-anderson
description: Saiba como criar serviços de back-end usando o ASP.NET Core MVC para dar suporte a aplicativos móveis nativos.
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564037"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="9b629-103">Criar serviços de back-end para aplicativos móveis nativos com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b629-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="9b629-104">Por [James Montemagno](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="9b629-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="9b629-105">Os aplicativos móveis podem se comunicar com os serviços de back-end do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b629-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="9b629-106">Para obter instruções sobre como conectar os serviços Web locais dos simuladores do iOS e dos emuladores do Android, confira [Conectar-se aos Serviços Web Locais em simuladores do iOS e emuladores do Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="9b629-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="9b629-107">Exibir ou baixar o código de exemplo dos serviços de back-end</span><span class="sxs-lookup"><span data-stu-id="9b629-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="9b629-108">Exemplo do aplicativo móvel nativo</span><span class="sxs-lookup"><span data-stu-id="9b629-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="9b629-109">Este tutorial demonstra como criar serviços de back-end usando o ASP.NET Core para dar suporte a aplicativos móveis nativos.</span><span class="sxs-lookup"><span data-stu-id="9b629-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="9b629-110">Ele usa o [aplicativo Xamarin. Forms TodoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) como seu cliente nativo, que inclui clientes nativos separados para Android, Ios e Windows.</span><span class="sxs-lookup"><span data-stu-id="9b629-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="9b629-111">Siga o tutorial com links para criar o aplicativo nativo (e instale as ferramentas do Xamarin gratuitas necessárias), além de baixar a solução de exemplo do Xamarin.</span><span class="sxs-lookup"><span data-stu-id="9b629-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="9b629-112">O exemplo do Xamarin inclui um ASP.NET Core projeto de serviços de API Web, que o aplicativo ASP.NET Core deste artigo substitui (sem alterações exigidas pelo cliente).</span><span class="sxs-lookup"><span data-stu-id="9b629-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Aplicativo ToDoRest em execução em um smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="9b629-114">Recursos</span><span class="sxs-lookup"><span data-stu-id="9b629-114">Features</span></span>

<span data-ttu-id="9b629-115">O [aplicativo TodoREST](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) dá suporte à listagem, adição, exclusão e atualização de itens de To-Do.</span><span class="sxs-lookup"><span data-stu-id="9b629-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="9b629-116">Cada item tem uma ID, um Nome, Observações e uma propriedade que indica se ele já foi Concluído.</span><span class="sxs-lookup"><span data-stu-id="9b629-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="9b629-117">A exibição principal dos itens, conforme mostrado acima, lista o nome de cada item e indica se ele foi concluído com uma marca de seleção.</span><span class="sxs-lookup"><span data-stu-id="9b629-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="9b629-118">Tocar no ícone `+` abre uma caixa de diálogo de adição de itens:</span><span class="sxs-lookup"><span data-stu-id="9b629-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Caixa de diálogo de adição de itens](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="9b629-120">Tocar em um item na tela da lista principal abre uma caixa de diálogo de edição, na qual o Nome do item, Observações e configurações de Concluído podem ser modificados, ou o item pode ser excluído:</span><span class="sxs-lookup"><span data-stu-id="9b629-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Caixa de diálogo de edição de itens](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="9b629-122">Para testá-lo em relação ao aplicativo ASP.NET Core criado na próxima seção em execução no computador, atualize a constante do aplicativo [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) .</span><span class="sxs-lookup"><span data-stu-id="9b629-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="9b629-123">Os emuladores do Android não são executados no computador local e usam um IP de loopback (10.0.2.2) para se comunicar com o computador local.</span><span class="sxs-lookup"><span data-stu-id="9b629-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="9b629-124">Aproveite o [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) para detectar em qual operação o sistema está sendo executado para usar a URL correta.</span><span class="sxs-lookup"><span data-stu-id="9b629-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="9b629-125">Navegue até o [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) projeto e abra o [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) arquivo.</span><span class="sxs-lookup"><span data-stu-id="9b629-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="9b629-126">O arquivo *Constants.cs* contém a configuração a seguir.</span><span class="sxs-lookup"><span data-stu-id="9b629-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="9b629-127">Opcionalmente, você pode implantar o serviço Web em um serviço de nuvem, como o Azure, e atualizar o `RestUrl` .</span><span class="sxs-lookup"><span data-stu-id="9b629-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="9b629-128">Criando o projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b629-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="9b629-129">Crie um novo aplicativo Web do ASP.NET Core no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b629-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="9b629-130">Escolha o modelo de API Web.</span><span class="sxs-lookup"><span data-stu-id="9b629-130">Choose the Web API template.</span></span> <span data-ttu-id="9b629-131">Nomeie o projeto *TodoAPI*.</span><span class="sxs-lookup"><span data-stu-id="9b629-131">Name the project *TodoAPI*.</span></span>

![Caixa de diálogo nova do aplicativo Web ASP.NET com modelo de projeto de Web API selecionado](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="9b629-133">O aplicativo deve responder a todas as solicitações feitas à porta 5000, incluindo o tráfego HTTP de texto não criptografado para nosso cliente móvel.</span><span class="sxs-lookup"><span data-stu-id="9b629-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="9b629-134">Atualize *Startup.cs* para que <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> não seja executado no desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="9b629-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="9b629-135">Execute o aplicativo diretamente, em vez de por trás de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="9b629-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="9b629-136">O IIS Express ignora as solicitações não locais por padrão.</span><span class="sxs-lookup"><span data-stu-id="9b629-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="9b629-137">Execute [dotnet executado](/dotnet/core/tools/dotnet-run) em um prompt de comando ou escolha o perfil de nome do aplicativo no menu suspenso de destino de depuração na barra de ferramentas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b629-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="9b629-138">Adicione uma classe de modelo para representar itens pendentes.</span><span class="sxs-lookup"><span data-stu-id="9b629-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="9b629-139">Marque os campos obrigatórios com o `[Required]` atributo:</span><span class="sxs-lookup"><span data-stu-id="9b629-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="9b629-140">Os métodos da API exigem alguma maneira de trabalhar com dados.</span><span class="sxs-lookup"><span data-stu-id="9b629-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="9b629-141">Use a mesma interface `ITodoRepository` nos usos de exemplo originais do Xamarin:</span><span class="sxs-lookup"><span data-stu-id="9b629-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="9b629-142">Para esta amostra, a implementação apenas usa uma coleção particular de itens:</span><span class="sxs-lookup"><span data-stu-id="9b629-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="9b629-143">Configure a implementação em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9b629-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="9b629-144">Criando o controlador</span><span class="sxs-lookup"><span data-stu-id="9b629-144">Creating the Controller</span></span>

<span data-ttu-id="9b629-145">Adicione um novo controlador ao projeto, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span><span class="sxs-lookup"><span data-stu-id="9b629-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="9b629-146">Ele deve herdar de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="9b629-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="9b629-147">Adicione um atributo `Route` para indicar que o controlador manipulará as solicitações feitas para caminhos que começam com `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="9b629-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="9b629-148">O token `[controller]` na rota é substituído pelo nome do controlador (com a omissão do sufixo `Controller`) e é especialmente útil para rotas globais.</span><span class="sxs-lookup"><span data-stu-id="9b629-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="9b629-149">Saiba mais sobre o [roteamento](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="9b629-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="9b629-150">O controlador requer um `ITodoRepository` para a função; solicite uma instância desse tipo usando o construtor do controlador.</span><span class="sxs-lookup"><span data-stu-id="9b629-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="9b629-151">No runtime, esta instância será fornecida com suporte do framework para[injeção de dependência](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="9b629-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="9b629-152">Essa API é compatível com quatro verbos HTTP diferentes para executar operações CRUD (Criar, Ler, Atualizar, Excluir) na fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="9b629-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="9b629-153">A mais simples delas é a operação Read, que corresponde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9b629-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="9b629-154">Lendo itens</span><span class="sxs-lookup"><span data-stu-id="9b629-154">Reading Items</span></span>

<span data-ttu-id="9b629-155">A solicitação de uma lista de itens é feita com uma solicitação GET ao método `List`.</span><span class="sxs-lookup"><span data-stu-id="9b629-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="9b629-156">O atributo `[HttpGet]` no método `List` indica que esta ação só deve lidar com as solicitações GET.</span><span class="sxs-lookup"><span data-stu-id="9b629-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="9b629-157">A rota para esta ação é a rota especificada no controlador.</span><span class="sxs-lookup"><span data-stu-id="9b629-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="9b629-158">Você não precisa necessariamente usar o nome da ação como parte da rota.</span><span class="sxs-lookup"><span data-stu-id="9b629-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="9b629-159">Você precisa garantir que cada ação tem uma rota exclusiva e não ambígua.</span><span class="sxs-lookup"><span data-stu-id="9b629-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="9b629-160">Os atributos de roteamento podem ser aplicados nos níveis de método e controlador para criar rotas específicas.</span><span class="sxs-lookup"><span data-stu-id="9b629-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="9b629-161">O `List` método retorna um código de resposta de 200 OK e todos os itens de tarefas pendentes, serializados como JSON.</span><span class="sxs-lookup"><span data-stu-id="9b629-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="9b629-162">Você pode testar o novo método de API usando uma variedade de ferramentas, como [Postman](https://www.getpostman.com/docs/). Veja abaixo:</span><span class="sxs-lookup"><span data-stu-id="9b629-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Console Postman mostrando uma solicitação GET para todoitems e corpo da resposta mostrando JSON para três itens retornados](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="9b629-164">Criando itens</span><span class="sxs-lookup"><span data-stu-id="9b629-164">Creating Items</span></span>

<span data-ttu-id="9b629-165">Por convenção, a criação de novos itens de dados é mapeada para o verbo HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="9b629-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="9b629-166">O `Create` método tem um `[HttpPost]` atributo aplicado a ele e aceita uma `TodoItem` instância.</span><span class="sxs-lookup"><span data-stu-id="9b629-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="9b629-167">Como o `item` argumento é passado no corpo da postagem, esse parâmetro especifica o `[FromBody]` atributo.</span><span class="sxs-lookup"><span data-stu-id="9b629-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="9b629-168">Dentro do método, o item é verificado quanto à validade e existência anterior no armazenamento de dados e, se nenhum problema ocorrer, ele será adicionado usando o repositório.</span><span class="sxs-lookup"><span data-stu-id="9b629-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="9b629-169">A verificação de `ModelState.IsValid` executa a [validação do modelo](../mvc/models/validation.md) e deve ser feita em todos os métodos de API que aceitam a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="9b629-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="9b629-170">O exemplo usa um `enum` que contém códigos de erro que são passados para o cliente móvel:</span><span class="sxs-lookup"><span data-stu-id="9b629-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="9b629-171">Teste a adição de novos itens usando Postman escolhendo o verbo POST fornecendo o novo objeto no formato JSON no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9b629-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="9b629-172">Você também deve adicionar um cabeçalho de solicitação que especifica um `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="9b629-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Console Postman mostrando um POST e resposta](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="9b629-174">O método retorna o item recém-criado na resposta.</span><span class="sxs-lookup"><span data-stu-id="9b629-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="9b629-175">Atualizando itens</span><span class="sxs-lookup"><span data-stu-id="9b629-175">Updating Items</span></span>

<span data-ttu-id="9b629-176">A modificação de registros é feita com as solicitações HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9b629-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="9b629-177">Além desta mudança, o método `Edit` é quase idêntico ao `Create`.</span><span class="sxs-lookup"><span data-stu-id="9b629-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="9b629-178">Observe que, se o registro não for encontrado, a ação `Edit` retornará uma resposta `NotFound` (404).</span><span class="sxs-lookup"><span data-stu-id="9b629-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="9b629-179">Para testar com Postman, altere o verbo para PUT.</span><span class="sxs-lookup"><span data-stu-id="9b629-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="9b629-180">Especifique os dados do objeto atualizado no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9b629-180">Specify the updated object data in the Body of the request.</span></span>

![Console Postman mostrando um PUT e resposta](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="9b629-182">Este método retornará uma resposta `NoContent` (204) quando obtiver êxito, para manter a consistência com a API já existente.</span><span class="sxs-lookup"><span data-stu-id="9b629-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="9b629-183">Excluindo itens</span><span class="sxs-lookup"><span data-stu-id="9b629-183">Deleting Items</span></span>

<span data-ttu-id="9b629-184">A exclusão de registros é feita por meio da criação de solicitações de exclusão para o serviço e por meio do envio do ID do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="9b629-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="9b629-185">Assim como as atualizações, as solicitações de itens que não existem receberão respostas `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="9b629-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="9b629-186">Caso contrário, uma solicitação bem-sucedida receberá uma resposta `NoContent` (204).</span><span class="sxs-lookup"><span data-stu-id="9b629-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="9b629-187">Observe que, ao testar a funcionalidade de exclusão, nada é necessário no Corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9b629-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Console do Postman mostrando um DELETE e uma resposta](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="9b629-189">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="9b629-189">Prevent over-posting</span></span>

<span data-ttu-id="9b629-190">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="9b629-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="9b629-191">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="9b629-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="9b629-192">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="9b629-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="9b629-193">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="9b629-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="9b629-194">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="9b629-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="9b629-195">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="9b629-195">A DTO may be used to:</span></span>

* <span data-ttu-id="9b629-196">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="9b629-196">Prevent over-posting.</span></span>
* <span data-ttu-id="9b629-197">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="9b629-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="9b629-198">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="9b629-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="9b629-199">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="9b629-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="9b629-200">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="9b629-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="9b629-201">Para demonstrar a abordagem do DTO, consulte [evitar excesso de postagem](xref:tutorials/first-web-api#prevent-over-posting)</span><span class="sxs-lookup"><span data-stu-id="9b629-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="9b629-202">Convenções de Web API comuns</span><span class="sxs-lookup"><span data-stu-id="9b629-202">Common Web API Conventions</span></span>

<span data-ttu-id="9b629-203">À medida que você desenvolve serviços de back-end para seu aplicativo, desejará criar um conjunto consistente de convenções ou políticas para lidar com preocupações paralelas.</span><span class="sxs-lookup"><span data-stu-id="9b629-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="9b629-204">Por exemplo, no serviço mostrado acima, as solicitações de registros específicos que não foram encontrados receberam uma resposta `NotFound`, em vez de uma resposta `BadRequest`.</span><span class="sxs-lookup"><span data-stu-id="9b629-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="9b629-205">Da mesma forma, os comandos feitos para esse serviço que passaram tipos associados a um modelo sempre verificaram `ModelState.IsValid` e retornaram um `BadRequest` para tipos de modelo inválidos.</span><span class="sxs-lookup"><span data-stu-id="9b629-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="9b629-206">Depois de identificar uma diretiva comum para suas APIs, você geralmente pode encapsulá-la em um [filtro](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="9b629-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="9b629-207">Saiba mais sobre [como encapsular políticas comuns da API em aplicativos ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="9b629-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b629-208">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9b629-208">Additional resources</span></span>

- [<span data-ttu-id="9b629-209">Xamarin. Forms: autenticação do serviço Web</span><span class="sxs-lookup"><span data-stu-id="9b629-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="9b629-210">Xamarin. Forms: consumir um serviço Web RESTful</span><span class="sxs-lookup"><span data-stu-id="9b629-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="9b629-211">Microsoft Learn: consumir serviços Web REST em aplicativos Xamarin</span><span class="sxs-lookup"><span data-stu-id="9b629-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="9b629-212">Microsoft Learn: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b629-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
