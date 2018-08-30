---
title: Visão geral do ASP.NET Core MVC
author: ardalis
description: Saiba como o ASP.NET Core MVC é uma estrutura avançada para a criação de aplicativos Web e APIs usando o padrão de design Model-View-Controller.
ms.author: riande
ms.date: 01/08/2018
uid: mvc/overview
ms.openlocfilehash: d2a50e48c20fe69b1fe691bfc9c91a27d4219922
ms.sourcegitcommit: 5a2456cbf429069dc48aaa2823cde14100e4c438
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "41902593"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="068b0-103">Visão geral do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="068b0-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="068b0-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="068b0-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="068b0-105">O ASP.NET Core MVC é uma estrutura avançada para a criação de aplicativos Web e APIs usando o padrão de design Model-View-Controller.</span><span class="sxs-lookup"><span data-stu-id="068b0-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="068b0-106">O que é o padrão MVC?</span><span class="sxs-lookup"><span data-stu-id="068b0-106">What is the MVC pattern?</span></span>

<span data-ttu-id="068b0-107">O padrão de arquitetura MVC (Model-View-Controller) separa um aplicativo em três grupos de componentes principais: Modelos, Exibições e Componentes.</span><span class="sxs-lookup"><span data-stu-id="068b0-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="068b0-108">Esse padrão ajuda a obter a [separação de interesses](http://deviq.com/separation-of-concerns/).</span><span class="sxs-lookup"><span data-stu-id="068b0-108">This pattern helps to achieve [separation of concerns](http://deviq.com/separation-of-concerns/).</span></span> <span data-ttu-id="068b0-109">Usando esse padrão, as solicitações de usuário são encaminhadas para um Controlador, que é responsável por trabalhar com o Modelo para executar as ações do usuário e/ou recuperar os resultados de consultas.</span><span class="sxs-lookup"><span data-stu-id="068b0-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="068b0-110">O Controlador escolhe a Exibição a ser exibida para o usuário e fornece-a com os dados do Modelo solicitados.</span><span class="sxs-lookup"><span data-stu-id="068b0-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="068b0-111">O seguinte diagrama mostra os três componentes principais e quais deles referenciam os outros:</span><span class="sxs-lookup"><span data-stu-id="068b0-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![Padrão MVC](overview/_static/mvc.png)

<span data-ttu-id="068b0-113">Essa descrição das responsabilidades ajuda você a dimensionar o aplicativo em termos de complexidade, porque é mais fácil de codificar, depurar e testar algo (modelo, exibição ou controlador) que tem um único trabalho (e que segue o [Princípio da Responsabilidade Única](http://deviq.com/single-responsibility-principle/)).</span><span class="sxs-lookup"><span data-stu-id="068b0-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job (and follows the [Single Responsibility Principle](http://deviq.com/single-responsibility-principle/)).</span></span> <span data-ttu-id="068b0-114">É mais difícil atualizar, testar e depurar um código que tem dependências distribuídas em duas ou mais dessas três áreas.</span><span class="sxs-lookup"><span data-stu-id="068b0-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="068b0-115">Por exemplo, a lógica da interface do usuário tende a ser alterada com mais frequência do que a lógica de negócios.</span><span class="sxs-lookup"><span data-stu-id="068b0-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="068b0-116">Se o código de apresentação e a lógica de negócios forem combinados em um único objeto, um objeto que contém a lógica de negócios precisa ser modificado sempre que a interface do usuário é alterada.</span><span class="sxs-lookup"><span data-stu-id="068b0-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="068b0-117">Isso costuma introduzir erros e exige um novo teste da lógica de negócios após cada alteração mínima da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="068b0-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="068b0-118">A exibição e o controlador dependem do modelo.</span><span class="sxs-lookup"><span data-stu-id="068b0-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="068b0-119">No entanto, o modelo não depende da exibição nem do controlador.</span><span class="sxs-lookup"><span data-stu-id="068b0-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="068b0-120">Esse é um dos principais benefícios da separação.</span><span class="sxs-lookup"><span data-stu-id="068b0-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="068b0-121">Essa separação permite que o modelo seja criado e testado de forma independente da apresentação visual.</span><span class="sxs-lookup"><span data-stu-id="068b0-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="068b0-122">Responsabilidades do Modelo</span><span class="sxs-lookup"><span data-stu-id="068b0-122">Model Responsibilities</span></span>

<span data-ttu-id="068b0-123">O Modelo em um aplicativo MVC representa o estado do aplicativo e qualquer lógica de negócios ou operação que deve ser executada por ele.</span><span class="sxs-lookup"><span data-stu-id="068b0-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="068b0-124">A lógica de negócios deve ser encapsulada no modelo, juntamente com qualquer lógica de implementação, para persistir o estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="068b0-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="068b0-125">As exibições fortemente tipadas normalmente usam tipos ViewModel criados para conter os dados a serem exibidos nessa exibição.</span><span class="sxs-lookup"><span data-stu-id="068b0-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="068b0-126">O controlador cria e popula essas instâncias de ViewModel com base no modelo.</span><span class="sxs-lookup"><span data-stu-id="068b0-126">The controller creates and populates these ViewModel instances from the model.</span></span>

> [!NOTE]
> <span data-ttu-id="068b0-127">Há várias maneiras de organizar o modelo em um aplicativo que usa o padrão de arquitetura MVC.</span><span class="sxs-lookup"><span data-stu-id="068b0-127">There are many ways to organize the model in an app that uses the MVC architectural pattern.</span></span> <span data-ttu-id="068b0-128">Saiba mais sobre alguns [tipos diferentes de tipos de modelo](http://deviq.com/kinds-of-models/).</span><span class="sxs-lookup"><span data-stu-id="068b0-128">Learn more about some [different kinds of model types](http://deviq.com/kinds-of-models/).</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="068b0-129">Responsabilidades da Exibição</span><span class="sxs-lookup"><span data-stu-id="068b0-129">View Responsibilities</span></span>

<span data-ttu-id="068b0-130">As exibições são responsáveis por apresentar o conteúdo por meio da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="068b0-130">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="068b0-131">Elas usam o [mecanismo de exibição do Razor](#razor-view-engine) para inserir o código .NET em uma marcação HTML.</span><span class="sxs-lookup"><span data-stu-id="068b0-131">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="068b0-132">Deve haver uma lógica mínima nas exibições e qualquer lógica contida nelas deve se relacionar à apresentação do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="068b0-132">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="068b0-133">Se você precisar executar uma grande quantidade de lógica em arquivos de exibição para exibir dados de um modelo complexo, considere o uso de um [Componente de Exibição](views/view-components.md), ViewModel ou um modelo de exibição para simplificar a exibição.</span><span class="sxs-lookup"><span data-stu-id="068b0-133">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="068b0-134">Responsabilidades do Controlador</span><span class="sxs-lookup"><span data-stu-id="068b0-134">Controller Responsibilities</span></span>

<span data-ttu-id="068b0-135">Os controladores são os componentes que cuidam da interação do usuário, trabalham com o modelo e, em última análise, selecionam uma exibição a ser renderizada.</span><span class="sxs-lookup"><span data-stu-id="068b0-135">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="068b0-136">Em um aplicativo MVC, a exibição mostra apenas informações; o controlador manipula e responde à entrada e à interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="068b0-136">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="068b0-137">No padrão MVC, o controlador é o ponto de entrada inicial e é responsável por selecionar quais tipos de modelo serão usados para o trabalho e qual exibição será renderizada (daí seu nome – ele controla como o aplicativo responde a determinada solicitação).</span><span class="sxs-lookup"><span data-stu-id="068b0-137">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="068b0-138">Os controladores não devem ser excessivamente complicados por muitas responsabilidades.</span><span class="sxs-lookup"><span data-stu-id="068b0-138">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="068b0-139">Para evitar que a lógica do controlador se torne excessivamente complexa, use o [Princípio da Responsabilidade Única](http://deviq.com/single-responsibility-principle/) para empurrar a lógica de negócios para fora do controlador e inseri-la no modelo de domínio.</span><span class="sxs-lookup"><span data-stu-id="068b0-139">To keep controller logic from becoming overly complex, use the [Single Responsibility Principle](http://deviq.com/single-responsibility-principle/) to push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="068b0-140">Se você achar que as ações do controlador executam com frequência os mesmos tipos de ações, siga o [Princípio Don't Repeat Yourself](http://deviq.com/don-t-repeat-yourself/) movendo essas ações comuns para [filtros](#filters).</span><span class="sxs-lookup"><span data-stu-id="068b0-140">If you find that your controller actions frequently perform the same kinds of actions, you can follow the [Don't Repeat Yourself principle](http://deviq.com/don-t-repeat-yourself/) by moving these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="068b0-141">O que é ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="068b0-141">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="068b0-142">A estrutura do ASP.NET Core MVC é uma estrutura de apresentação leve, de software livre e altamente testável, otimizada para uso com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="068b0-142">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="068b0-143">ASP.NET Core MVC fornece uma maneira com base em padrões para criar sites dinâmicos que habilitam uma separação limpa de preocupações.</span><span class="sxs-lookup"><span data-stu-id="068b0-143">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="068b0-144">Ele lhe dá controle total sobre a marcação, dá suporte ao desenvolvimento amigável a TDD e usa os padrões da web mais recentes.</span><span class="sxs-lookup"><span data-stu-id="068b0-144">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="068b0-145">Recursos</span><span class="sxs-lookup"><span data-stu-id="068b0-145">Features</span></span>

<span data-ttu-id="068b0-146">ASP.NET Core MVC inclui o seguinte:</span><span class="sxs-lookup"><span data-stu-id="068b0-146">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="068b0-147">Roteamento</span><span class="sxs-lookup"><span data-stu-id="068b0-147">Routing</span></span>](#routing)
* [<span data-ttu-id="068b0-148">Associação de modelos</span><span class="sxs-lookup"><span data-stu-id="068b0-148">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="068b0-149">Validação de modelo</span><span class="sxs-lookup"><span data-stu-id="068b0-149">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="068b0-150">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="068b0-150">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="068b0-151">Filtros</span><span class="sxs-lookup"><span data-stu-id="068b0-151">Filters</span></span>](#filters)
* [<span data-ttu-id="068b0-152">Áreas</span><span class="sxs-lookup"><span data-stu-id="068b0-152">Areas</span></span>](#areas)
* [<span data-ttu-id="068b0-153">APIs Web</span><span class="sxs-lookup"><span data-stu-id="068b0-153">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="068b0-154">Capacidade de teste</span><span class="sxs-lookup"><span data-stu-id="068b0-154">Testability</span></span>](#testability)
* [<span data-ttu-id="068b0-155">Mecanismo de exibição do Razor</span><span class="sxs-lookup"><span data-stu-id="068b0-155">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="068b0-156">Exibições fortemente tipadas</span><span class="sxs-lookup"><span data-stu-id="068b0-156">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="068b0-157">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="068b0-157">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="068b0-158">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="068b0-158">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="068b0-159">Roteamento</span><span class="sxs-lookup"><span data-stu-id="068b0-159">Routing</span></span>

<span data-ttu-id="068b0-160">O ASP.NET Core MVC baseia-se no [roteamento do ASP.NET Core](../fundamentals/routing.md), um componente de mapeamento de URL avançado que permite criar aplicativos que têm URLs compreensíveis e pesquisáveis.</span><span class="sxs-lookup"><span data-stu-id="068b0-160">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="068b0-161">Isso permite que você defina padrões de nomenclatura de URL do aplicativo que funcionam bem para SEO (otimização do mecanismo de pesquisa) e para a geração de links, sem levar em consideração como os arquivos no servidor Web estão organizados.</span><span class="sxs-lookup"><span data-stu-id="068b0-161">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="068b0-162">Defina as rotas usando uma sintaxe de modelo de rota conveniente que dá suporte a restrições de valor de rota, padrões e valores opcionais.</span><span class="sxs-lookup"><span data-stu-id="068b0-162">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="068b0-163">O *roteamento baseado em convenção* permite definir globalmente os formatos de URL aceitos pelo aplicativo e como cada um desses formatos é mapeado para um método de ação específico em determinado controlador.</span><span class="sxs-lookup"><span data-stu-id="068b0-163">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="068b0-164">Quando uma solicitação de entrada é recebida, o mecanismo de roteamento analisa a URL e corresponde-a a um dos formatos de URL definidos. Em seguida, ele chama o método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="068b0-164">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="068b0-165">O *roteamento de atributo* permite que você especifique as informações de roteamento decorando os controladores e as ações com atributos que definem as rotas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="068b0-165">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="068b0-166">Isso significa que as definições de rota são colocadas ao lado do controlador e da ação aos quais estão associadas.</span><span class="sxs-lookup"><span data-stu-id="068b0-166">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a><span data-ttu-id="068b0-167">Associação de modelos</span><span class="sxs-lookup"><span data-stu-id="068b0-167">Model binding</span></span>

<span data-ttu-id="068b0-168">ASP.NET Core MVC [associação de modelo](models/model-binding.md) converte dados de solicitação de cliente (valores de formulário, os dados de rota, parâmetros de cadeia de caracteres de consulta, os cabeçalhos HTTP) em objetos que o controlador pode manipular.</span><span class="sxs-lookup"><span data-stu-id="068b0-168">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="068b0-169">Como resultado, a lógica de controlador não precisa fazer o trabalho de descobrir os dados de solicitação de entrada; ele simplesmente tem os dados como parâmetros para os métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="068b0-169">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a><span data-ttu-id="068b0-170">Validação de modelo</span><span class="sxs-lookup"><span data-stu-id="068b0-170">Model validation</span></span>

<span data-ttu-id="068b0-171">O ASP.NET Core MVC dá suporte à [validação](models/validation.md) pela decoração do objeto de modelo com atributos de validação de anotação de dados.</span><span class="sxs-lookup"><span data-stu-id="068b0-171">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="068b0-172">Os atributos de validação são verificados no lado do cliente antes que os valores sejam postados no servidor, bem como no servidor antes que a ação do controlador seja chamada.</span><span class="sxs-lookup"><span data-stu-id="068b0-172">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="068b0-173">Uma ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="068b0-173">A controller action:</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="068b0-174">A estrutura manipula a validação dos dados de solicitação no cliente e no servidor.</span><span class="sxs-lookup"><span data-stu-id="068b0-174">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="068b0-175">A lógica de validação especificada em tipos de modelo é adicionada às exibições renderizados como anotações não invasivas e é imposta no navegador com o [jQuery Validation](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="068b0-175">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="068b0-176">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="068b0-176">Dependency injection</span></span>

<span data-ttu-id="068b0-177">O ASP.NET Core tem suporte interno para [DI (injeção de dependência)](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="068b0-177">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="068b0-178">No ASP.NET Core MVC, os [controladores](controllers/dependency-injection.md) podem solicitar serviços necessários por meio de seus construtores, possibilitando o acompanhamento do [princípio de dependências explícitas](http://deviq.com/explicit-dependencies-principle/).</span><span class="sxs-lookup"><span data-stu-id="068b0-178">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/).</span></span>

<span data-ttu-id="068b0-179">O aplicativo também pode usar a [injeção de dependência em arquivos no exibição](views/dependency-injection.md), usando a diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="068b0-179">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

```cshtml
@inject SomeService ServiceName
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="068b0-180">Filtros</span><span class="sxs-lookup"><span data-stu-id="068b0-180">Filters</span></span>

<span data-ttu-id="068b0-181">Os [filtros](controllers/filters.md) ajudam os desenvolvedores a encapsular interesses paralelos, como tratamento de exceção ou autorização.</span><span class="sxs-lookup"><span data-stu-id="068b0-181">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="068b0-182">Os filtros permitem a execução de uma lógica pré e pós-processamento personalizada para métodos de ação e podem ser configurados para execução em determinados pontos no pipeline de execução de uma solicitação específica.</span><span class="sxs-lookup"><span data-stu-id="068b0-182">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="068b0-183">Os filtros podem ser aplicados a controladores ou ações como atributos (ou podem ser executados globalmente).</span><span class="sxs-lookup"><span data-stu-id="068b0-183">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="068b0-184">Vários filtros (como `Authorize`) são incluídos na estrutura.</span><span class="sxs-lookup"><span data-stu-id="068b0-184">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="068b0-185">`[Authorize]` é o atributo usado para criar filtros de autorização do MVC.</span><span class="sxs-lookup"><span data-stu-id="068b0-185">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
   public class AccountController : Controller
   {
```

### <a name="areas"></a><span data-ttu-id="068b0-186">Áreas</span><span class="sxs-lookup"><span data-stu-id="068b0-186">Areas</span></span>

<span data-ttu-id="068b0-187">As [áreas](controllers/areas.md) fornecem uma maneira de particionar um aplicativo Web ASP.NET Core MVC grande em agrupamentos funcionais menores.</span><span class="sxs-lookup"><span data-stu-id="068b0-187">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="068b0-188">Uma área é uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="068b0-188">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="068b0-189">Em um projeto MVC, componentes lógicos como Modelo, Controlador e Exibição são mantidos em pastas diferentes e o MVC usa convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="068b0-189">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="068b0-190">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="068b0-190">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="068b0-191">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa, etc. Cada uma dessas unidades têm suas próprias exibições de componente lógico, controladores e modelos.</span><span class="sxs-lookup"><span data-stu-id="068b0-191">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="068b0-192">APIs da Web</span><span class="sxs-lookup"><span data-stu-id="068b0-192">Web APIs</span></span>

<span data-ttu-id="068b0-193">Além de ser uma ótima plataforma para a criação de sites, o ASP.NET Core MVC tem um excelente suporte para a criação de APIs Web.</span><span class="sxs-lookup"><span data-stu-id="068b0-193">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="068b0-194">Crie serviços que alcançam uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="068b0-194">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="068b0-195">A estrutura inclui suporte para a negociação de conteúdo HTTP com suporte interno para [formatar dados](xref:web-api/advanced/formatting) como JSON ou XML.</span><span class="sxs-lookup"><span data-stu-id="068b0-195">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="068b0-196">Escreva [formatadores personalizados](xref:web-api/advanced/custom-formatters) para adicionar suporte para seus próprios formatos.</span><span class="sxs-lookup"><span data-stu-id="068b0-196">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="068b0-197">Use a geração de links para habilitar o suporte para hipermídia.</span><span class="sxs-lookup"><span data-stu-id="068b0-197">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="068b0-198">Habilite o suporte para o [CORS (Compartilhamento de Recursos Entre Origens)](http://www.w3.org/TR/cors/) com facilidade, de modo que as APIs Web possam ser compartilhadas entre vários aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="068b0-198">Easily enable support for [cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="068b0-199">Capacidade de teste</span><span class="sxs-lookup"><span data-stu-id="068b0-199">Testability</span></span>

<span data-ttu-id="068b0-200">O uso pela estrutura da injeção de dependência e de interfaces a torna adequada para teste de unidade. Além disso, a estrutura inclui recursos (como um provedor TestHost e InMemory para o Entity Framework) que também agiliza e facilita a execução de [testes de integração](xref:test/integration-tests).</span><span class="sxs-lookup"><span data-stu-id="068b0-200">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="068b0-201">Saiba mais sobre [como testar a lógica do controlador](controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="068b0-201">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="razor-view-engine"></a><span data-ttu-id="068b0-202">Mecanismo de exibição do Razor</span><span class="sxs-lookup"><span data-stu-id="068b0-202">Razor view engine</span></span>

<span data-ttu-id="068b0-203">As [exibições do ASP.NET Core MVC](views/overview.md) usam o [mecanismo de exibição do Razor](views/razor.md) para renderizar exibições.</span><span class="sxs-lookup"><span data-stu-id="068b0-203">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="068b0-204">Razor é uma linguagem de marcação de modelo compacta, expressiva e fluida para definir exibições usando um código C# inserido.</span><span class="sxs-lookup"><span data-stu-id="068b0-204">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="068b0-205">O Razor é usado para gerar o conteúdo da Web no servidor de forma dinâmica.</span><span class="sxs-lookup"><span data-stu-id="068b0-205">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="068b0-206">Você pode combinar o código do servidor com o código e o conteúdo do lado cliente de maneira limpa.</span><span class="sxs-lookup"><span data-stu-id="068b0-206">You can cleanly mix server code with client side content and code.</span></span>

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

<span data-ttu-id="068b0-207">Usando o mecanismo de exibição do Razor, você pode definir [layouts](views/layout.md), [exibições parciais](views/partial.md) e seções substituíveis.</span><span class="sxs-lookup"><span data-stu-id="068b0-207">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="068b0-208">Exibições fortemente tipadas</span><span class="sxs-lookup"><span data-stu-id="068b0-208">Strongly typed views</span></span>

<span data-ttu-id="068b0-209">As exibições do Razor no MVC podem ser fortemente tipadas com base no modelo.</span><span class="sxs-lookup"><span data-stu-id="068b0-209">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="068b0-210">Os controladores podem passar um modelo fortemente tipado para as exibições, permitindo que elas tenham a verificação de tipo e o suporte do IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="068b0-210">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="068b0-211">Por exemplo, a seguinte exibição renderiza um modelo do tipo `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="068b0-211">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="068b0-212">Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="068b0-212">Tag Helpers</span></span>

<span data-ttu-id="068b0-213">Os [Auxiliares de Marca](views/tag-helpers/intro.md) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="068b0-213">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="068b0-214">Use auxiliares de marca para definir marcas personalizadas (por exemplo, `<environment>`) ou para modificar o comportamento de marcas existentes (por exemplo, `<label>`).</span><span class="sxs-lookup"><span data-stu-id="068b0-214">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="068b0-215">Os Auxiliares de Marca associam a elementos específicos com base no nome do elemento e seus atributos.</span><span class="sxs-lookup"><span data-stu-id="068b0-215">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="068b0-216">Eles oferecem os benefícios da renderização do lado do servidor, enquanto preservam uma experiência de edição de HTML.</span><span class="sxs-lookup"><span data-stu-id="068b0-216">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="068b0-217">Há muitos Auxiliares de Marca internos para tarefas comuns – como criação de formulários, links, carregamento de ativos e muito mais – e ainda outros disponíveis em repositórios GitHub públicos e como NuGet.</span><span class="sxs-lookup"><span data-stu-id="068b0-217">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="068b0-218">Os Auxiliares de Marca são criados no C# e são direcionados a elementos HTML de acordo com o nome do elemento, o nome do atributo ou a marca pai.</span><span class="sxs-lookup"><span data-stu-id="068b0-218">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="068b0-219">Por exemplo, o LinkTagHelper interno pode ser usado para criar um link para a ação `Login` do `AccountsController`:</span><span class="sxs-lookup"><span data-stu-id="068b0-219">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="068b0-220">O `EnvironmentTagHelper` pode ser usado para incluir scripts diferentes nas exibições (por exemplo, bruto ou minimizado) de acordo com o ambiente de tempo de execução, como Desenvolvimento, Preparo ou Produção:</span><span class="sxs-lookup"><span data-stu-id="068b0-220">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="068b0-221">Os Auxiliares de Marca fornecem uma experiência de desenvolvimento amigável a HTML e um ambiente avançado do IntelliSense para a criação de HTML e marcação do Razor.</span><span class="sxs-lookup"><span data-stu-id="068b0-221">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="068b0-222">A maioria dos Auxiliares de Marca internos é direcionada a elementos HTML existentes e fornece atributos do lado do servidor para o elemento.</span><span class="sxs-lookup"><span data-stu-id="068b0-222">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="068b0-223">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="068b0-223">View Components</span></span>

<span data-ttu-id="068b0-224">Os [Componentes de Exibição](views/view-components.md) permitem que você empacote a lógica de renderização e reutilize-a em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="068b0-224">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="068b0-225">São semelhantes às [exibições parciais](views/partial.md), mas com a lógica associada.</span><span class="sxs-lookup"><span data-stu-id="068b0-225">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="068b0-226">Versão de compatibilidade</span><span class="sxs-lookup"><span data-stu-id="068b0-226">Compatibility version</span></span>

<span data-ttu-id="068b0-227">O método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite que um aplicativo aceite ou recuse as possíveis alterações da falha de comportamento introduzidas no ASP.NET Core MVC 2.1 ou posteriores.</span><span class="sxs-lookup"><span data-stu-id="068b0-227">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="068b0-228">Para obter mais informações, consulte <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="068b0-228">For more information, see <xref:mvc/compatibility-version>.</span></span>
