---
title: Parte 2, adicionar um controlador a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Parte 2 da série de tutoriais no ASP.NET Core MVC.
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975255"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="eb6c4-103">Parte 2, adicionar um controlador a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="eb6c4-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="eb6c4-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eb6c4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb6c4-105">O padrão de arquitetura MVC (Model-View-Controller) separa um aplicativo em três componentes principais: **M** odel, **V** iew e **C** ontroller.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="eb6c4-106">O padrão MVC ajuda a criar aplicativos que são mais testáveis e fáceis de atualizar comparado aos aplicativos monolíticos tradicionais.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="eb6c4-107">Os aplicativos baseados no MVC contêm:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="eb6c4-108">**M** odels: classes que representam os dados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="eb6c4-109">As classes de modelo usam a lógica de validação para impor regras de negócio aos dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="eb6c4-110">Normalmente, os objetos de modelo recuperam e armazenam o estado do modelo em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="eb6c4-111">Neste tutorial, um modelo `Movie` recupera dados de filmes de um banco de dados, fornece-os para a exibição ou atualiza-os.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="eb6c4-112">O dados atualizados são gravados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="eb6c4-113">**V** iews: exibições são os componentes que exibem a interface do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="eb6c4-114">Em geral, essa interface do usuário exibe os dados de modelo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="eb6c4-115">**C** Ontrollers: classes que:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="eb6c4-116">Manipular solicitações do navegador.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-116">Handle browser requests.</span></span>
  * <span data-ttu-id="eb6c4-117">Recuperar dados de modelo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-117">Retrieve model data.</span></span>
  * <span data-ttu-id="eb6c4-118">Chame modelos de exibição que retornam uma resposta.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-118">Call view templates that return a response.</span></span>

<span data-ttu-id="eb6c4-119">Em um aplicativo MVC, o modo de exibição exibe apenas informações.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="eb6c4-120">O controlador manipula e responde à entrada e à interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="eb6c4-121">Por exemplo, o controlador manipula os segmentos de URL e os valores de cadeia de caracteres de consulta e passa esses valores para o modelo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="eb6c4-122">O modelo pode usar esses valores para consultar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-122">The model might use these values to query the database.</span></span> <span data-ttu-id="eb6c4-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-123">For example:</span></span>

* <span data-ttu-id="eb6c4-124">`Https://localhost:5001/Home/Privacy`: especifica o `Home`  controlador e a `Privacy` ação.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="eb6c4-125">`Https://localhost:5001/Movies/Edit/5`: é uma solicitação para editar o filme com ID = 5 usando o `Movies` controlador e a `Edit` ação, que são detalhados posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="eb6c4-126">Os dados de rota são explicados posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="eb6c4-127">O padrão de arquitetura MVC separa um aplicativo em três grupos principais de componentes: modelos, exibições e controladores.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="eb6c4-128">Esse padrão ajuda a obter a separação das preocupações: a lógica da interface do usuário pertence à exibição.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="eb6c4-129">A lógica de entrada pertence ao controlador.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="eb6c4-130">A lógica de negócios pertence ao modelo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-130">Business logic belongs in the model.</span></span> <span data-ttu-id="eb6c4-131">Essa separação ajuda a gerenciar a complexidade ao criar um aplicativo, pois ele permite trabalhar em um aspecto da implementação de cada vez sem afetar o código de outro.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="eb6c4-132">Por exemplo, você pode trabalhar no código de exibição sem depender do código da lógica de negócios.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="eb6c4-133">Esses conceitos são apresentados e demonstrados nesta série de tutoriais durante a criação de um aplicativo de filme.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="eb6c4-134">O projeto MVC contém pastas para os *Controladores* e as *Exibições*.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="eb6c4-135">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="eb6c4-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb6c4-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb6c4-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb6c4-137">No **Gerenciador de soluções**, clique com o botão direito do mouse em **controladores > adicionar > controlador**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![Gerenciador de Soluções, clique com o botão direito do mouse em controladores > adicionar > controlador](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="eb6c4-139">Na caixa de diálogo **Adicionar Scaffold** , selecione **controlador MVC – vazio**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![Adicionar o controlador MVC e nomeá-lo](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="eb6c4-141">Na **caixa de diálogo Adicionar novo item-MvcMovie**, digite **HelloWorldController.cs** e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb6c4-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb6c4-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eb6c4-143">Selecione o ícone **EXPLORER** e, em seguida, pressione Control (clique com o botão direito do mouse) **Controladores > Novo Arquivo** e nomeie o novo arquivo *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![Menu contextual](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb6c4-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eb6c4-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb6c4-146">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores > Adicionar > Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Menu contextual](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="eb6c4-148">Selecione **ASP.NET Core** e **classe de controlador**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="eb6c4-149">Nomeie o controlador **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-149">Name the controller **HelloWorldController**.</span></span>

![Adicionar o controlador MVC e nomeá-lo](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="eb6c4-151">Substitua o conteúdo de *Controllers/HelloWorldController.cs* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="eb6c4-152">Cada método `public` em um controlador pode ser chamado como um ponto de extremidade HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="eb6c4-153">Na amostra acima, ambos os métodos retornam uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="eb6c4-154">Observe os comentários que precedem cada método.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="eb6c4-155">Um ponto de extremidade HTTP:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="eb6c4-156">É uma URL de destino no aplicativo Web, como `https://localhost:5001/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="eb6c4-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="eb6c4-157">Combinada</span><span class="sxs-lookup"><span data-stu-id="eb6c4-157">Combines:</span></span>
  * <span data-ttu-id="eb6c4-158">O protocolo usado: `HTTPS` .</span><span class="sxs-lookup"><span data-stu-id="eb6c4-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="eb6c4-159">O local de rede do servidor Web, incluindo a porta TCP: `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="eb6c4-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="eb6c4-160">O URI de destino: `HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="eb6c4-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="eb6c4-161">O primeiro comentário indica que este é um método [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) invocado por meio do acréscimo de `/HelloWorld/` à URL base.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="eb6c4-162">O primeiro comentário especifica um método [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) invocado por meio do acréscimo de `/HelloWorld/Welcome/` à URL base.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="eb6c4-163">Posteriormente no tutorial, o mecanismo scaffolding é usado para gerar `HTTP POST` métodos, que atualizam os dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="eb6c4-164">Execute o aplicativo sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-164">Run the app without the debugger.</span></span>

<span data-ttu-id="eb6c4-165">Acrescente "HelloWorld" ao caminho na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="eb6c4-166">O método `Index` retorna uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-166">The `Index` method returns a string.</span></span>

![A janela do navegador mostrando uma resposta de aplicativo dessa é minha ação padrão](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="eb6c4-168">O MVC invoca classes de controlador e os métodos de ação dentro deles, dependendo da URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="eb6c4-169">A [lógica de roteamento de URL](xref:mvc/controllers/routing) padrão usada pelo MVC usa um formato como este para determinar qual código invocar:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="eb6c4-170">O formato de roteamento é definido no método `Configure` no arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="eb6c4-171">Quando você acessa o aplicativo e não fornece nenhum segmento de URL, ele usa como padrão o controlador “Home” e o método “Index” especificado na linha do modelo realçada acima.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="eb6c4-172">Nos segmentos de URL anteriores:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="eb6c4-173">O primeiro segmento de URL determina a classe do controlador a ser executada.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="eb6c4-174">Portanto, o `localhost:5001/HelloWorld` mapeia para a classe **HelloWorld** Controller.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="eb6c4-175">A segunda parte do segmento de URL determina o método de ação na classe.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="eb6c4-176">Portanto, `localhost:5001/HelloWorld/Index` faz com que o `Index` método da `HelloWorldController` classe seja executado.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="eb6c4-177">Observe que você precisou apenas navegar para `localhost:5001/HelloWorld` e o método `Index` foi chamado por padrão.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="eb6c4-178">`Index` é o método padrão que será chamado em um controlador se um nome de método não for explicitamente especificado.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="eb6c4-179">A terceira parte do segmento de URL (`id`) refere-se aos dados de rota.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="eb6c4-180">Os dados de rota são explicados posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="eb6c4-181">Navegue até: `https://localhost:{PORT}/HelloWorld/Welcome` .</span><span class="sxs-lookup"><span data-stu-id="eb6c4-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="eb6c4-182">Substitua `{PORT}` pelo número da porta.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="eb6c4-183">O método `Welcome` é executado e retorna a cadeia de caracteres `This is the Welcome action method...`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="eb6c4-184">Para essa URL, o controlador é `HelloWorld` e `Welcome` é o método de ação.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="eb6c4-185">Você ainda não usou a parte `[Parameters]` da URL.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Janela do navegador mostrando a resposta do aplicativo Este é o método de ação Boas-vindas](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="eb6c4-187">Modifique o código para passar algumas informações de parâmetro da URL para o controlador.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="eb6c4-188">Por exemplo, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="eb6c4-189">Altere o método `Welcome` para incluir dois parâmetros, conforme mostrado no código a seguir.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="eb6c4-190">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-190">The preceding code:</span></span>

* <span data-ttu-id="eb6c4-191">Usa o recurso de parâmetro opcional do C# para indicar que o parâmetro `numTimes` usa 1 como padrão se nenhum valor é passado para esse parâmetro.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="eb6c4-192">Usa `HtmlEncoder.Default.Encode` o para proteger o aplicativo contra entradas mal-intencionadas, como por meio de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="eb6c4-193">Usa [Cadeias de caracteres interpoladas](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) em `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="eb6c4-194">Execute o aplicativo e navegue até: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4` .</span><span class="sxs-lookup"><span data-stu-id="eb6c4-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="eb6c4-195">Substitua `{PORT}` pelo número da porta.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="eb6c4-196">Tente valores diferentes para `name` e `numtimes` na URL.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="eb6c4-197">O sistema de [Associação de modelo](xref:mvc/models/model-binding) do MVC mapeia automaticamente os parâmetros nomeados da cadeia de caracteres de consulta para parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="eb6c4-198">Consulte [Model binding](xref:mvc/models/model-binding) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Janela do navegador mostrando uma resposta do aplicativo de Hello Rick, NumTimes é \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="eb6c4-200">Na imagem anterior:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-200">In the previous image:</span></span>

* <span data-ttu-id="eb6c4-201">O segmento de URL `Parameters` não é usado.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="eb6c4-202">Os `name` `numTimes` parâmetros e são passados na [cadeia de caracteres de consulta](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="eb6c4-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="eb6c4-203">O `?` (ponto de interrogação) na URL acima é um separador e a cadeia de caracteres de consulta é a seguinte.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="eb6c4-204">O `&` caractere separa os pares campo-valor.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="eb6c4-205">Substitua o método `Welcome` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="eb6c4-206">Execute o aplicativo e insira a seguinte URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="eb6c4-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="eb6c4-207">Na URL anterior:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-207">In the preceding URL:</span></span>

* <span data-ttu-id="eb6c4-208">O terceiro segmento de URL correspondeu ao parâmetro de rota `id` .</span><span class="sxs-lookup"><span data-stu-id="eb6c4-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="eb6c4-209">O método `Welcome` contém um parâmetro `id` que correspondeu ao modelo de URL no método `MapControllerRoute`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="eb6c4-210">O `?` à direita (em `id?`) indica que o parâmetro `id` é opcional.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="eb6c4-211">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-211">In the preceding example:</span></span>

* <span data-ttu-id="eb6c4-212">O terceiro segmento de URL correspondeu ao parâmetro de rota `id` .</span><span class="sxs-lookup"><span data-stu-id="eb6c4-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="eb6c4-213">O método `Welcome` contém um parâmetro `id` que correspondeu ao modelo de URL no método `MapControllerRoute`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="eb6c4-214">O `?` à direita (em `id?`) indica que o parâmetro `id` é opcional.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="eb6c4-215">[Anterior:](start-mvc.md) 
>  introdução [Em seguida: adicionar uma exibição](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="eb6c4-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb6c4-216">O padrão de arquitetura MVC (Model-View-Controller) separa um aplicativo em três componentes principais: **M** odel, **V** iew e **C** ontroller.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="eb6c4-217">O padrão MVC ajuda a criar aplicativos que são mais testáveis e fáceis de atualizar comparado aos aplicativos monolíticos tradicionais.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="eb6c4-218">Os aplicativos baseados no MVC contêm:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="eb6c4-219">**M** odels: classes que representam os dados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="eb6c4-220">As classes de modelo usam a lógica de validação para impor regras de negócio aos dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="eb6c4-221">Normalmente, os objetos de modelo recuperam e armazenam o estado do modelo em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="eb6c4-222">Neste tutorial, um modelo `Movie` recupera dados de filmes de um banco de dados, fornece-os para a exibição ou atualiza-os.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="eb6c4-223">O dados atualizados são gravados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="eb6c4-224">**V** iews: exibições são os componentes que exibem a interface do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="eb6c4-225">Em geral, essa interface do usuário exibe os dados de modelo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="eb6c4-226">**C** ontrollers: classes que manipulam as solicitações do navegador.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="eb6c4-227">Elas recuperam dados de modelo e chamam modelos de exibição que retornam uma resposta.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="eb6c4-228">Em um aplicativo MVC, a exibição mostra apenas informações; o controlador manipula e responde à entrada e à interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="eb6c4-229">Por exemplo, o controlador manipula os dados de rota e os valores de cadeia de consulta e passa esses valores para o modelo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="eb6c4-230">O modelo pode usar esses valores para consultar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-230">The model might use these values to query the database.</span></span> <span data-ttu-id="eb6c4-231">Por exemplo, `https://localhost:5001/Home/About` tem dados de rota de `Home` (o controlador) e `About` (o método de ação a ser chamado no controlador principal).</span><span class="sxs-lookup"><span data-stu-id="eb6c4-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="eb6c4-232">`https://localhost:5001/Movies/Edit/5` é uma solicitação para editar o filme com ID=5 usando o controlador do filme.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="eb6c4-233">Os dados de rota são explicados posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="eb6c4-234">O padrão MVC ajuda a criar aplicativos que separam os diferentes aspectos do aplicativo (lógica de entrada, lógica de negócios e lógica da interface do usuário), ao mesmo tempo que fornece um acoplamento flexível entre esses elementos.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="eb6c4-235">O padrão especifica o local em que cada tipo de lógica deve estar localizado no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="eb6c4-236">A lógica da interface do usuário pertence à exibição.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="eb6c4-237">A lógica de entrada pertence ao controlador.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="eb6c4-238">A lógica de negócios pertence ao modelo.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-238">Business logic belongs in the model.</span></span> <span data-ttu-id="eb6c4-239">Essa separação ajuda a gerenciar a complexidade ao criar um aplicativo, porque permite que você trabalhe em um aspecto da implementação por vez, sem afetar o código de outro.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="eb6c4-240">Por exemplo, você pode trabalhar no código de exibição sem depender do código da lógica de negócios.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="eb6c4-241">Abrangemos esses conceitos nesta série de tutoriais e mostraremos como usá-los para criar um aplicativo de filme.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="eb6c4-242">O projeto MVC contém pastas para os *Controladores* e as *Exibições*.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="eb6c4-243">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="eb6c4-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb6c4-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb6c4-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eb6c4-245">Em **Gerenciador de soluções**, clique com o botão direito do mouse em **controladores > adicionar > controlador**</span><span class="sxs-lookup"><span data-stu-id="eb6c4-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![Menu contextual](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="eb6c4-247">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC – Vazio**</span><span class="sxs-lookup"><span data-stu-id="eb6c4-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![Adicionar o controlador MVC e nomeá-lo](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="eb6c4-249">Na **caixa de diálogo Adicionar Controlador MVC Vazio**, insira **HelloWorldController** e selecione **ADICIONAR**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb6c4-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb6c4-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eb6c4-251">Selecione o ícone **EXPLORER** e, em seguida, pressione Control (clique com o botão direito do mouse) **Controladores > Novo Arquivo** e nomeie o novo arquivo *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![Menu contextual](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb6c4-253">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eb6c4-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb6c4-254">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores > Adicionar > Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Menu contextual](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="eb6c4-256">Selecione **ASP.NET Core** e **Classe do Controlador MVC**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="eb6c4-257">Nomeie o controlador **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-257">Name the controller **HelloWorldController**.</span></span>

![Adicionar o controlador MVC e nomeá-lo](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="eb6c4-259">Substitua o conteúdo de *Controllers/HelloWorldController.cs* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="eb6c4-260">Cada método `public` em um controlador pode ser chamado como um ponto de extremidade HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="eb6c4-261">Na amostra acima, ambos os métodos retornam uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="eb6c4-262">Observe os comentários que precedem cada método.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="eb6c4-263">Um ponto de extremidade HTTP é uma URL direcionável no aplicativo Web, como `https://localhost:5001/HelloWorld`, e combina o protocolo usado `HTTPS`, o local de rede do servidor Web (incluindo a porta TCP) `localhost:5001` e o URI de destino `HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="eb6c4-264">O primeiro comentário indica que este é um método [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) invocado por meio do acréscimo de `/HelloWorld/` à URL base.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="eb6c4-265">O primeiro comentário especifica um método [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) invocado por meio do acréscimo de `/HelloWorld/Welcome/` à URL base.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="eb6c4-266">Mais adiante no tutorial, o mecanismo de scaffolding será usado para gerar métodos `HTTP POST` que atualizam dados.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="eb6c4-267">Execute o aplicativo no modo sem depuração e acrescente “HelloWorld” ao caminho na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="eb6c4-268">O método `Index` retorna uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-268">The `Index` method returns a string.</span></span>

![Janela do navegador mostrando a resposta do aplicativo Esta é minha ação padrão](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="eb6c4-270">O MVC invoca as classes do controlador (e os métodos de ação dentro delas), dependendo da URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="eb6c4-271">A [lógica de roteamento de URL](xref:mvc/controllers/routing) padrão usada pelo MVC usa um formato como este para determinar o código a ser invocado:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="eb6c4-272">O formato de roteamento é definido no método `Configure` no arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="eb6c4-273">Quando você acessa o aplicativo e não fornece nenhum segmento de URL, ele usa como padrão o controlador “Home” e o método “Index” especificado na linha do modelo realçada acima.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="eb6c4-274">O primeiro segmento de URL determina a classe do controlador a ser executada.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="eb6c4-275">Portanto, `localhost:{PORT}/HelloWorld` é mapeado para a classe `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="eb6c4-276">A segunda parte do segmento de URL determina o método de ação na classe.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="eb6c4-277">Portanto, `localhost:{PORT}/HelloWorld/Index` fará com que o método `Index` da classe `HelloWorldController` seja executado.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="eb6c4-278">Observe que você precisou apenas navegar para `localhost:{PORT}/HelloWorld` e o método `Index` foi chamado por padrão.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="eb6c4-279">Isso ocorre porque `Index` é o método padrão que será chamado em um controlador se um nome de método não for especificado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="eb6c4-280">A terceira parte do segmento de URL (`id`) refere-se aos dados de rota.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="eb6c4-281">Os dados de rota são explicados posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="eb6c4-282">Navegue até `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="eb6c4-283">O método `Welcome` é executado e retorna a cadeia de caracteres `This is the Welcome action method...`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="eb6c4-284">Para essa URL, o controlador é `HelloWorld` e `Welcome` é o método de ação.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="eb6c4-285">Você ainda não usou a parte `[Parameters]` da URL.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Janela do navegador mostrando a resposta do aplicativo Este é o método de ação Boas-vindas](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="eb6c4-287">Modifique o código para passar algumas informações de parâmetro da URL para o controlador.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="eb6c4-288">Por exemplo, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="eb6c4-289">Altere o método `Welcome` para incluir dois parâmetros, conforme mostrado no código a seguir.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="eb6c4-290">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-290">The preceding code:</span></span>

* <span data-ttu-id="eb6c4-291">Usa o recurso de parâmetro opcional do C# para indicar que o parâmetro `numTimes` usa 1 como padrão se nenhum valor é passado para esse parâmetro.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="eb6c4-292">Usa `HtmlEncoder.Default.Encode` para proteger o aplicativo contra entrada mal-intencionada (ou seja, JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb6c4-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="eb6c4-293">Usa [Cadeias de caracteres interpoladas](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) em `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="eb6c4-294">Execute o aplicativo e navegue até:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="eb6c4-295">(Substitua `{PORT}` pelo número da porta.) Você pode experimentar valores diferentes para `name` e `numtimes` na URL.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="eb6c4-296">O sistema de [model binding](xref:mvc/models/model-binding) do MVC mapeia automaticamente os parâmetros nomeados da cadeia de consulta na barra de endereços para os parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="eb6c4-297">Consulte [Model binding](xref:mvc/models/model-binding) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Janela do navegador mostrando uma resposta do aplicativo de Hello Rick, NumTimes é \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="eb6c4-299">Na imagem acima, o segmento de URL ( `Parameters` ) não é usado, `name` os `numTimes` parâmetros e são passados na [cadeia de caracteres de consulta](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="eb6c4-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="eb6c4-300">O `?` (ponto de interrogação) na URL acima é um separador e a cadeia de caracteres de consulta é a seguinte.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="eb6c4-301">O `&` caractere separa os pares campo-valor.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="eb6c4-302">Substitua o método `Welcome` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="eb6c4-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="eb6c4-303">Execute o aplicativo e insira a seguinte URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="eb6c4-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="eb6c4-304">Agora, o terceiro segmento de URL correspondeu ao parâmetro de rota `id`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="eb6c4-305">O método `Welcome` contém um parâmetro `id` que correspondeu ao modelo de URL no método `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="eb6c4-306">O `?` à direita (em `id?`) indica que o parâmetro `id` é opcional.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="eb6c4-307">Nestes exemplos, o controlador faz a parte “VC” do MVC – ou seja, o trabalho da exibição e do controlador.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="eb6c4-308">O controlador retorna o HTML diretamente.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="eb6c4-309">Em geral, você não deseja que os controladores retornem HTML diretamente, pois isso é muito difícil de codificar e manter.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="eb6c4-310">Em vez disso, você normalmente usa um Razor arquivo de modelo de exibição separado para ajudar a gerar a resposta HTML.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="eb6c4-311">Faça isso no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="eb6c4-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="eb6c4-312">[Anterior](start-mvc.md) 
>  [Avançar](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="eb6c4-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
