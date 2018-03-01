---
title: "Criando serviços de back-end para aplicativos móveis nativos"
author: ardalis
description: 
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mobile/native-mobile-backend
ms.openlocfilehash: ff09f331cff5cca7b42fa89bff55c0ed5c7d82f4
ms.sourcegitcommit: 18d1dc86770f2e272d93c7e1cddfc095c5995d9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2018
---
# <a name="creating-backend-services-for-native-mobile-applications"></a><span data-ttu-id="6f3cf-102">Criando serviços de back-end para aplicativos móveis nativos</span><span class="sxs-lookup"><span data-stu-id="6f3cf-102">Creating Backend Services for Native Mobile Applications</span></span>

<span data-ttu-id="6f3cf-103">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6f3cf-103">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6f3cf-104">Os aplicativos móveis podem se comunicar com facilidade com os serviços de back-end do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-104">Mobile apps can easily communicate with ASP.NET Core backend services.</span></span>

[<span data-ttu-id="6f3cf-105">Exibir ou baixar o código de exemplo de serviços de back-end</span><span class="sxs-lookup"><span data-stu-id="6f3cf-105">View or download sample backend services code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="6f3cf-106">Exemplo do aplicativo móvel nativo </span><span class="sxs-lookup"><span data-stu-id="6f3cf-106">The Sample Native Mobile App</span></span>

<span data-ttu-id="6f3cf-107">Este tutorial demonstra como criar serviços de back-end usando o ASP.NET Core MVC para dar suporte a aplicativos móveis nativos.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-107">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="6f3cf-108">Ele usa o [aplicativo Xamarin Forms ToDoRest](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) como seu cliente nativo, que inclui clientes nativos separados para dispositivos Android, iOS, Universal do Windows e Windows Phone.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-108">It uses the [Xamarin Forms ToDoRest app](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="6f3cf-109">Siga o tutorial com links para criar o aplicativo nativo (e instale as ferramentas do Xamarin gratuitas necessárias), além de baixar a solução de exemplo do Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-109">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="6f3cf-110">A amostra do Xamarin inclui um projeto de serviços da API Web ASP.NET 2, que substitui o aplicativo ASP.NET Core deste artigo (sem nenhuma alteração exigida pelo cliente).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-110">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Aplicativo To Do Rest em execução em um smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="6f3cf-112">Recursos</span><span class="sxs-lookup"><span data-stu-id="6f3cf-112">Features</span></span>

<span data-ttu-id="6f3cf-113">O aplicativo ToDoRest é compatível com listagem, adição, exclusão e atualização de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-113">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="6f3cf-114">Cada item tem uma ID, um Nome, Observações e uma propriedade que indica se ele já foi Concluído.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-114">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="6f3cf-115">A exibição principal dos itens, conforme mostrado acima, lista o nome de cada item e indica se ele foi concluído com uma marca de seleção.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-115">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="6f3cf-116">Tocar no ícone `+` abre uma caixa de diálogo de adição de itens:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-116">Tapping the `+` icon opens an add item dialog:</span></span>

![Caixa de diálogo de adição de itens](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="6f3cf-118">Tocar em um item na tela da lista principal abre uma caixa de diálogo de edição, na qual o Nome do item, Observações e configurações de Concluído podem ser modificados, ou o item pode ser excluído:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-118">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Caixa de diálogo de edição de itens](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="6f3cf-120">Esta amostra é configurada por padrão para usar os serviços de back-end hospedados em developer.xamarin.com, que permitem operações somente leitura.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-120">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="6f3cf-121">Para testá-la por conta própria no aplicativo ASP.NET Core criado na próxima seção em execução no computador, você precisará atualizar a constante `RestUrl` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-121">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="6f3cf-122">Navegue para o projeto `ToDoREST` e abra o arquivo *Constants.cs*.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-122">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="6f3cf-123">Substitua o `RestUrl` por uma URL que inclui o endereço IP do computador (não localhost ou 127.0.0.1, pois esse endereço é usado no emulador do dispositivo, não no computador).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-123">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="6f3cf-124">Inclua o número da porta também (5000).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-124">Include the port number as well (5000).</span></span> <span data-ttu-id="6f3cf-125">Para testar se os serviços funcionam com um dispositivo, verifique se você não tem um firewall ativo bloqueando o acesso a essa porta.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-125">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="6f3cf-126">Criando o projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f3cf-126">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="6f3cf-127">Crie um novo aplicativo Web do ASP.NET Core no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-127">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="6f3cf-128">Escolha o modelo de Web API sem autenticação.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-128">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="6f3cf-129">Nomeie o projeto como *ToDoApi*.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-129">Name the project *ToDoApi*.</span></span>

![Caixa de diálogo nova do aplicativo Web ASP.NET com modelo de projeto de Web API selecionado](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="6f3cf-131">O aplicativo deve responder a todas as solicitações feitas através da porta 5000.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-131">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="6f3cf-132">Atualize o *Program.cs* para incluir `.UseUrls("http://*:5000")` para ficar assim:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-132">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="6f3cf-133">Execute o aplicativo diretamente, em vez de por trás do IIS Express, que ignora solicitações não local por padrão.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-133">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="6f3cf-134">Execute `dotnet run` em um prompt de comando ou escolha o perfil de nome do aplicativo no menu suspenso de destino de depuração na barra de ferramentas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-134">Run `dotnet run` from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="6f3cf-135">Adicione uma classe de modelo para representar itens pendentes</span><span class="sxs-lookup"><span data-stu-id="6f3cf-135">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="6f3cf-136">Marque os campos obrigatórios usando o atributo `[Required]`:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-136">Mark required fields using the `[Required]` attribute:</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="6f3cf-137">Os métodos da API exigem alguma maneira de trabalhar com os dados.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-137">The API methods require some way to work with data.</span></span> <span data-ttu-id="6f3cf-138">Use a mesma interface `IToDoRepository` usada pelo exemplo original do Xamarin:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-138">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="6f3cf-139">Para esta amostra, a implementação apenas usa uma coleção particular de itens:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-139">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="6f3cf-140">Configure a implementação em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-140">Configure the implementation in *Startup.cs*:</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="6f3cf-141">Neste ponto, você está pronto para criar o *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-141">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="6f3cf-142">Saiba mais sobre como criar APIs da Web em [criando sua primeira API da Web com ASP.NET Core MVC e Visual Studio](../tutorials/first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-142">Learn more about creating web APIs in [Building Your First Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="6f3cf-143">Criando o controlador</span><span class="sxs-lookup"><span data-stu-id="6f3cf-143">Creating the Controller</span></span>

<span data-ttu-id="6f3cf-144">Adicione um novo controlador ao projeto, *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-144">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="6f3cf-145">Ele deve herdar de Microsoft.AspNetCore.Mvc.Controller.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-145">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="6f3cf-146">Adicione um atributo `Route` para indicar que o controlador manipulará as solicitações feitas para caminhos que começam com `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-146">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="6f3cf-147">O token `[controller]` na rota é substituído pelo nome do controlador (com a omissão do sufixo `Controller`) e é especialmente útil para rotas globais.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-147">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="6f3cf-148">Saiba mais sobre o [roteamento](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-148">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="6f3cf-149">O controlador requer um `IToDoRepository` para a função; solicite uma instância desse tipo usando o construtor do controlador.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-149">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="6f3cf-150">No tempo de execução, esta instância será fornecida com suporte do framework para[injeção de dependência](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-150">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="6f3cf-151">Essa API é compatível com quatro verbos HTTP diferentes para executar operações CRUD (Criar, Ler, Atualizar, Excluir) na fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-151">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="6f3cf-152">A mais simples delas é a operação Read, que corresponde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-152">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="6f3cf-153">Lendo itens</span><span class="sxs-lookup"><span data-stu-id="6f3cf-153">Reading Items</span></span>

<span data-ttu-id="6f3cf-154">A solicitação de uma lista de itens é feita com uma solicitação GET ao método `List`.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-154">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="6f3cf-155">O atributo `[HttpGet]` no método `List` indica que esta ação só deve lidar com as solicitações GET</span><span class="sxs-lookup"><span data-stu-id="6f3cf-155">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="6f3cf-156">A rota para esta ação é a rota especificada no controlador.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-156">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="6f3cf-157">Você não precisa necessariamente usar o nome da ação como parte da rota.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-157">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="6f3cf-158">Você precisa garantir que cada ação tem uma rota exclusiva e não ambígua.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-158">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="6f3cf-159">Os atributos de roteamento podem ser aplicados nos níveis de método e controlador para criar rotas específicas.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-159">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="6f3cf-160">O método `List` retorna um código de resposta OK 200 e todos os itens de tarefas, serializados como JSON.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-160">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="6f3cf-161">Você pode testar o novo método de API usando uma variedade de ferramentas, como [Postman](https://www.getpostman.com/docs/), conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-161">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Console Postman mostrando uma solicitação GET para todoitems e corpo da resposta mostrando JSON para três itens retornados](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="6f3cf-163">Criando itens</span><span class="sxs-lookup"><span data-stu-id="6f3cf-163">Creating Items</span></span>

<span data-ttu-id="6f3cf-164">Por convenção, a criação de novos itens de dados é mapeada para o verbo HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-164">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="6f3cf-165">O método `Create` tem um atributo `[HttpPost]` aplicado a ele e aceita uma instância `ToDoItem`.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-165">The `Create` method has an `[HttpPost]` attribute applied to it, and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="6f3cf-166">Como o argumento `item` será enviado no corpo de POST, este parâmetro será decorado com o atributo `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-166">Since the `item` argument will be passed in the body of the POST, this parameter is decorated with the `[FromBody]` attribute.</span></span>

<span data-ttu-id="6f3cf-167">Dentro do método, o item é verificado quanto à validade e existência anterior no armazenamento de dados e, se nenhum problema ocorrer, ele será adicionado usando o repositório.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-167">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="6f3cf-168">A verificação de `ModelState.IsValid` executa a [validação do modelo](../mvc/models/validation.md) e deve ser feita em todos os métodos de API que aceitam a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-168">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="6f3cf-169">A amostra usa uma enumeração que contém códigos de erro que são passados para o cliente móvel:</span><span class="sxs-lookup"><span data-stu-id="6f3cf-169">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="6f3cf-170">Teste a adição de novos itens usando Postman escolhendo o verbo POST fornecendo o novo objeto no formato JSON no corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="6f3cf-170">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="6f3cf-171">Você também deve adicionar um cabeçalho de solicitação que especifica um `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-171">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Console Postman mostrando um POST e resposta](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="6f3cf-173">O método retorna o item recém-criado na resposta.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-173">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="6f3cf-174">Atualizando itens</span><span class="sxs-lookup"><span data-stu-id="6f3cf-174">Updating Items</span></span>

<span data-ttu-id="6f3cf-175">A modificação de registros é feita com as solicitações HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-175">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="6f3cf-176">Além desta mudança, o método `Edit` é quase idêntico ao `Create`.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-176">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="6f3cf-177">Observe que, se o registro não for encontrado, a ação `Edit` retornará uma resposta `NotFound` (404).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-177">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="6f3cf-178">Para testar com Postman, altere o verbo para PUT.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-178">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="6f3cf-179">Especifique os dados do objeto atualizado no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-179">Specify the updated object data in the Body of the request.</span></span>

![Console Postman mostrando um PUT e resposta](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="6f3cf-181">Este método retornará uma resposta `NoContent` (204) quando obtiver êxito, para manter a consistência com a API já existente.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-181">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="6f3cf-182">Excluindo itens</span><span class="sxs-lookup"><span data-stu-id="6f3cf-182">Deleting Items</span></span>

<span data-ttu-id="6f3cf-183">A exclusão de registros é feita por meio da criação de solicitações de exclusão para o serviço e por meio do envio do ID do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-183">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="6f3cf-184">Assim como as atualizações, as solicitações de itens que não existem receberão respostas `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-184">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="6f3cf-185">Caso contrário, uma solicitação bem-sucedida receberá uma resposta `NoContent` (204).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-185">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="6f3cf-186">Observe que, ao testar a funcionalidade de exclusão, nada é necessário no Corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-186">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Console do Postman mostrando um DELETE e uma resposta](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="6f3cf-188">Convenções de Web API comuns</span><span class="sxs-lookup"><span data-stu-id="6f3cf-188">Common Web API Conventions</span></span>

<span data-ttu-id="6f3cf-189">À medida que você desenvolve serviços de back-end para seu aplicativo, desejará criar um conjunto consistente de convenções ou políticas para lidar com preocupações paralelas.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-189">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="6f3cf-190">Por exemplo, no serviço mostrado acima, as solicitações de registros específicos que não foram encontrados receberam uma resposta `NotFound`, em vez de uma resposta `BadRequest`.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-190">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="6f3cf-191">Da mesma forma, os comandos feitos para esse serviço que passaram tipos associados a um modelo sempre verificaram `ModelState.IsValid` e retornaram um `BadRequest` para tipos de modelo inválidos.</span><span class="sxs-lookup"><span data-stu-id="6f3cf-191">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="6f3cf-192">Depois de identificar uma política comum para suas APIs, você geralmente pode encapsulá-la em um [filtro](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-192">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="6f3cf-193">Saiba mais sobre [como encapsular políticas comuns da API em aplicativos ASP.NET Core MVC](https://msdn.microsoft.com/magazine/mt767699.aspx).</span><span class="sxs-lookup"><span data-stu-id="6f3cf-193">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](https://msdn.microsoft.com/magazine/mt767699.aspx).</span></span>
