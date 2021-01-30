---
title: Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core
author: stevejgordon
description: Saiba mais sobre como usar a interface IHttpClientFactory para gerenciar instâncias de HttpClient lógicas no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057324"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="75e6f-103">Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75e6f-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="75e6f-104">Por [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc)e [Ryan Nowak](https://github.com/rynowak).</span><span class="sxs-lookup"><span data-stu-id="75e6f-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="75e6f-105">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="75e6f-106">`IHttpClientFactory` oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="75e6f-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="75e6f-107">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-108">Por exemplo, um cliente chamado  *GitHub* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="75e6f-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="75e6f-109">Um cliente padrão pode ser registrado para acesso geral.</span><span class="sxs-lookup"><span data-stu-id="75e6f-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="75e6f-110">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="75e6f-111">Fornece extensões para o middleware baseado em Polly para tirar proveito da delegação de manipuladores no `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="75e6f-112">Gerencia o pooling e o tempo de vida de instâncias subjacentes `HttpClientMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="75e6f-113">O gerenciamento automático evita problemas comuns de DNS (sistema de nomes de domínio) que ocorrem ao gerenciar manualmente os `HttpClient` tempos de vida.</span><span class="sxs-lookup"><span data-stu-id="75e6f-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="75e6f-114">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="75e6f-115">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="75e6f-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="75e6f-116">O código de exemplo nesta versão do tópico usa <xref:System.Text.Json> para desserializar o conteúdo JSON retornado em respostas http.</span><span class="sxs-lookup"><span data-stu-id="75e6f-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="75e6f-117">Para obter exemplos que usam `Json.NET` e `ReadAsAsync<T>` , use o seletor de versão para selecionar uma versão 2. x deste tópico.</span><span class="sxs-lookup"><span data-stu-id="75e6f-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="75e6f-118">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="75e6f-118">Consumption patterns</span></span>

<span data-ttu-id="75e6f-119">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="75e6f-120">Uso básico</span><span class="sxs-lookup"><span data-stu-id="75e6f-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="75e6f-121">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="75e6f-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="75e6f-122">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="75e6f-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="75e6f-123">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="75e6f-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="75e6f-124">A melhor abordagem depende dos requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="75e6f-125">Uso básico</span><span class="sxs-lookup"><span data-stu-id="75e6f-125">Basic usage</span></span>

<span data-ttu-id="75e6f-126">`IHttpClientFactory` pode ser registrado chamando `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="75e6f-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="75e6f-127">Um `IHttpClientFactory` pode ser solicitado usando [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="75e6f-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="75e6f-128">O código a seguir usa `IHttpClientFactory` para criar uma `HttpClient` instância do:</span><span class="sxs-lookup"><span data-stu-id="75e6f-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="75e6f-129">Usar `IHttpClientFactory` como no exemplo anterior é uma boa maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="75e6f-130">Ele não tem impacto sobre como o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="75e6f-131">Em locais onde `HttpClient` as instâncias são criadas em um aplicativo existente, substitua essas ocorrências por chamadas para <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="75e6f-132">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="75e6f-132">Named clients</span></span>

<span data-ttu-id="75e6f-133">Os clientes nomeados são uma boa opção quando:</span><span class="sxs-lookup"><span data-stu-id="75e6f-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="75e6f-134">O aplicativo requer muitos usos distintos do `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="75e6f-135">Muitos `HttpClient` s têm configuração diferente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="75e6f-136">A configuração de um nome `HttpClient` pode ser especificada durante o registro em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="75e6f-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="75e6f-137">No código anterior, o cliente está configurado com:</span><span class="sxs-lookup"><span data-stu-id="75e6f-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="75e6f-138">O endereço base `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="75e6f-139">Dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="75e6f-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="75e6f-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="75e6f-140">CreateClient</span></span>

<span data-ttu-id="75e6f-141">Cada vez <xref:System.Net.Http.IHttpClientFactory.CreateClient*> é chamado:</span><span class="sxs-lookup"><span data-stu-id="75e6f-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="75e6f-142">Uma nova instância do `HttpClient` é criada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="75e6f-143">A ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-143">The configuration action is called.</span></span>

<span data-ttu-id="75e6f-144">Para criar um cliente nomeado, passe seu nome para `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="75e6f-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="75e6f-145">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="75e6f-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="75e6f-146">O código pode passar apenas o caminho, já que o endereço base configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="75e6f-147">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="75e6f-147">Typed clients</span></span>

<span data-ttu-id="75e6f-148">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-148">Typed clients:</span></span>

* <span data-ttu-id="75e6f-149">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="75e6f-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="75e6f-150">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="75e6f-151">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="75e6f-152">Por exemplo, um único cliente tipado pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="75e6f-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="75e6f-153">Para um ponto de extremidade de back-end único.</span><span class="sxs-lookup"><span data-stu-id="75e6f-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="75e6f-154">Para encapsular toda a lógica lidando com o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="75e6f-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="75e6f-155">Trabalhe com DI e pode ser injetado onde necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="75e6f-156">Um cliente tipado aceita um `HttpClient` parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="75e6f-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="75e6f-157">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="75e6f-157">In the preceding code:</span></span>

* <span data-ttu-id="75e6f-158">A configuração é movida para o cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="75e6f-159">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="75e6f-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="75e6f-160">Métodos específicos de API podem ser criados para expor a `HttpClient` funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="75e6f-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="75e6f-161">Por exemplo, o `GetAspNetDocsIssues` método encapsula o código para recuperar problemas abertos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="75e6f-162">O código a seguir <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> chama `Startup.ConfigureServices` para registrar uma classe de cliente tipada:</span><span class="sxs-lookup"><span data-stu-id="75e6f-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="75e6f-163">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="75e6f-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="75e6f-164">No código anterior, o `AddHttpClient` registra `GitHubService` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="75e6f-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="75e6f-165">Esse registro usa um método de fábrica para:</span><span class="sxs-lookup"><span data-stu-id="75e6f-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="75e6f-166">Crie uma instância de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="75e6f-167">Crie uma instância do `GitHubService` , passando a instância do `HttpClient` para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="75e6f-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="75e6f-168">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="75e6f-169">A configuração para um cliente tipado pode ser especificada durante o registro em `Startup.ConfigureServices` , em vez de no construtor do cliente digitado:</span><span class="sxs-lookup"><span data-stu-id="75e6f-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="75e6f-170">O `HttpClient` pode ser encapsulado em um cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="75e6f-171">Em vez de expô-lo como uma propriedade, defina um método que chame a `HttpClient` instância internamente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="75e6f-172">No código anterior, o `HttpClient` é armazenado em um campo particular.</span><span class="sxs-lookup"><span data-stu-id="75e6f-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="75e6f-173">O acesso ao `HttpClient` é pelo método público `GetRepos` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="75e6f-174">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="75e6f-174">Generated clients</span></span>

<span data-ttu-id="75e6f-175">`IHttpClientFactory` pode ser usado em combinação com bibliotecas de terceiros, como [rEFIt](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="75e6f-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="75e6f-176">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="75e6f-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="75e6f-177">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="75e6f-178">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="75e6f-179">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="75e6f-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="75e6f-180">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="75e6f-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="75e6f-181">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="75e6f-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="75e6f-182">Fazer solicitações POST, PUT e DELETE</span><span class="sxs-lookup"><span data-stu-id="75e6f-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="75e6f-183">Nos exemplos anteriores, todas as solicitações HTTP usam o verbo GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="75e6f-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="75e6f-184">`HttpClient` também dá suporte a outros verbos HTTP, incluindo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="75e6f-185">POST</span><span class="sxs-lookup"><span data-stu-id="75e6f-185">POST</span></span>
* <span data-ttu-id="75e6f-186">PUT</span><span class="sxs-lookup"><span data-stu-id="75e6f-186">PUT</span></span>
* <span data-ttu-id="75e6f-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="75e6f-187">DELETE</span></span>
* <span data-ttu-id="75e6f-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="75e6f-188">PATCH</span></span>

<span data-ttu-id="75e6f-189">Para obter uma lista completa de verbos HTTP com suporte, consulte <xref:System.Net.Http.HttpMethod> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="75e6f-190">O exemplo a seguir mostra como fazer uma solicitação HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="75e6f-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="75e6f-191">No código anterior, o `CreateItemAsync` método:</span><span class="sxs-lookup"><span data-stu-id="75e6f-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="75e6f-192">Serializa o `TodoItem` parâmetro para JSON usando `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="75e6f-193">Isso usa uma instância do <xref:System.Text.Json.JsonSerializerOptions> para configurar o processo de serialização.</span><span class="sxs-lookup"><span data-stu-id="75e6f-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="75e6f-194">Cria uma instância do <xref:System.Net.Http.StringContent> para empacotar o JSON serializado para envio no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="75e6f-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="75e6f-195">Chamadas <xref:System.Net.Http.HttpClient.PostAsync%2A> para enviar o conteúdo JSON para a URL especificada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="75e6f-196">Essa é uma URL relativa que é adicionada ao [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="75e6f-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="75e6f-197">Chamadas <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> para gerar uma exceção se o código de status de resposta não indicar êxito.</span><span class="sxs-lookup"><span data-stu-id="75e6f-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="75e6f-198">`HttpClient` também oferece suporte a outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="75e6f-199">Por exemplo, <xref:System.Net.Http.MultipartContent> e <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="75e6f-200">Para obter uma lista completa de conteúdo com suporte, consulte <xref:System.Net.Http.HttpContent> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="75e6f-201">O exemplo a seguir mostra uma solicitação HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="75e6f-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="75e6f-202">O código anterior é muito semelhante ao exemplo de POST.</span><span class="sxs-lookup"><span data-stu-id="75e6f-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="75e6f-203">O `SaveItemAsync` método chama <xref:System.Net.Http.HttpClient.PutAsync%2A> em vez de `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="75e6f-204">O exemplo a seguir mostra uma solicitação HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="75e6f-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="75e6f-205">No código anterior, o `DeleteItemAsync` método chama <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="75e6f-206">Como as solicitações HTTP DELETE normalmente não contêm corpo, o `DeleteAsync` método não fornece uma sobrecarga que aceita uma instância do `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="75e6f-207">Para saber mais sobre como usar verbos HTTP diferentes com o `HttpClient` , consulte <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="75e6f-208">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="75e6f-208">Outgoing request middleware</span></span>

<span data-ttu-id="75e6f-209">`HttpClient` tem o conceito de delegar manipuladores que podem ser vinculados juntos para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="75e6f-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="75e6f-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="75e6f-211">Simplifica a definição dos manipuladores a serem aplicados para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="75e6f-212">Dá suporte ao registro e encadeamento de vários manipuladores para criar um pipeline de middleware de solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="75e6f-213">Cada um desses manipuladores é capaz de realizar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="75e6f-214">Esse padrão:</span><span class="sxs-lookup"><span data-stu-id="75e6f-214">This pattern:</span></span>
  
    * <span data-ttu-id="75e6f-215">É semelhante ao pipeline de middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75e6f-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="75e6f-216">Fornece um mecanismo para gerenciar preocupações abrangentes em relação a solicitações HTTP, como:</span><span class="sxs-lookup"><span data-stu-id="75e6f-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="75e6f-217">cache</span><span class="sxs-lookup"><span data-stu-id="75e6f-217">caching</span></span>
      * <span data-ttu-id="75e6f-218">tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="75e6f-218">error handling</span></span>
      * <span data-ttu-id="75e6f-219">serialização</span><span class="sxs-lookup"><span data-stu-id="75e6f-219">serialization</span></span>
      * <span data-ttu-id="75e6f-220">registro em log</span><span class="sxs-lookup"><span data-stu-id="75e6f-220">logging</span></span>

<span data-ttu-id="75e6f-221">Para criar um manipulador de delegação:</span><span class="sxs-lookup"><span data-stu-id="75e6f-221">To create a delegating handler:</span></span>

* <span data-ttu-id="75e6f-222">Derivar de <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="75e6f-223">Substitua <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="75e6f-224">Execute o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="75e6f-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="75e6f-225">O código anterior verifica se o `X-API-KEY` cabeçalho está na solicitação.</span><span class="sxs-lookup"><span data-stu-id="75e6f-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="75e6f-226">Se `X-API-KEY` estiver ausente, <xref:System.Net.HttpStatusCode.BadRequest> será retornado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="75e6f-227">Mais de um manipulador pode ser adicionado à configuração para um `HttpClient` com <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="75e6f-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="75e6f-228">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="75e6f-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="75e6f-229">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="75e6f-230">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="75e6f-231">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="75e6f-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="75e6f-232">Usar DI no middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="75e6f-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="75e6f-233">Quando `IHttpClientFactory` o cria um novo manipulador de delegação, ele usa di para atender aos parâmetros do construtor do manipulador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="75e6f-234">`IHttpClientFactory` Cria um escopo de di **separado** para cada manipulador, o que pode levar a um comportamento surpreendente quando um manipulador consome um serviço com *escopo* .</span><span class="sxs-lookup"><span data-stu-id="75e6f-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="75e6f-235">Por exemplo, considere a seguinte interface e sua implementação, que representa uma tarefa como uma operação com um identificador, `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="75e6f-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="75e6f-236">Como o nome sugere, `IOperationScoped` é registrado com di usando um tempo de vida no *escopo* :</span><span class="sxs-lookup"><span data-stu-id="75e6f-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="75e6f-237">O manipulador de delegação a seguir consome e usa `IOperationScoped` para definir o `X-OPERATION-ID` cabeçalho para a solicitação de saída:</span><span class="sxs-lookup"><span data-stu-id="75e6f-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="75e6f-238">No [ `HttpRequestsSample` Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navegue até `/Operation` e atualize a página.</span><span class="sxs-lookup"><span data-stu-id="75e6f-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="75e6f-239">O valor do escopo da solicitação é alterado para cada solicitação, mas o valor do escopo do manipulador só é alterado a cada 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="75e6f-240">Os manipuladores podem depender dos serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="75e6f-241">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="75e6f-242">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="75e6f-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="75e6f-243">Passe dados para o manipulador usando [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="75e6f-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="75e6f-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="75e6f-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="75e6f-245">Crie um objeto de armazenamento <xref:System.Threading.AsyncLocal`1> personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="75e6f-246">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-246">Use Polly-based handlers</span></span>

<span data-ttu-id="75e6f-247">`IHttpClientFactory` integra-se com a biblioteca de terceiros [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="75e6f-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="75e6f-248">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="75e6f-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="75e6f-249">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="75e6f-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="75e6f-250">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-251">As extensões Polly dão suporte à adição de manipuladores baseados em Polly a clientes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="75e6f-252">Polly requer o pacote NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="75e6f-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="75e6f-253">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="75e6f-253">Handle transient faults</span></span>

<span data-ttu-id="75e6f-254">As falhas normalmente ocorrem quando chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="75e6f-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="75e6f-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permite que uma política seja definida para lidar com erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="75e6f-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="75e6f-256">Políticas configuradas com `AddTransientHttpErrorPolicy` o tratamento das seguintes respostas:</span><span class="sxs-lookup"><span data-stu-id="75e6f-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="75e6f-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="75e6f-257">HTTP 5xx</span></span>
* <span data-ttu-id="75e6f-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="75e6f-258">HTTP 408</span></span>

<span data-ttu-id="75e6f-259">`AddTransientHttpErrorPolicy` fornece acesso a um `PolicyBuilder` objeto configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="75e6f-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="75e6f-260">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="75e6f-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="75e6f-261">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="75e6f-262">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="75e6f-262">Dynamically select policies</span></span>

<span data-ttu-id="75e6f-263">Os métodos de extensão são fornecidos para adicionar manipuladores baseados em Polly, por exemplo, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="75e6f-264">A sobrecarga a seguir `AddPolicyHandler` inspeciona a solicitação para decidir qual política aplicar:</span><span class="sxs-lookup"><span data-stu-id="75e6f-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="75e6f-265">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="75e6f-266">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="75e6f-267">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="75e6f-268">É comum aninhar políticas Polly:</span><span class="sxs-lookup"><span data-stu-id="75e6f-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="75e6f-269">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="75e6f-269">In the preceding example:</span></span>

* <span data-ttu-id="75e6f-270">Dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-270">Two handlers are added.</span></span>
* <span data-ttu-id="75e6f-271">O primeiro manipulador usa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="75e6f-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="75e6f-272">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="75e6f-273">A segunda `AddTransientHttpErrorPolicy` chamada adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="75e6f-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="75e6f-274">Outras solicitações externas serão bloqueadas por 30 segundos se 5 tentativas com falha ocorrerem em sequência.</span><span class="sxs-lookup"><span data-stu-id="75e6f-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="75e6f-275">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="75e6f-276">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="75e6f-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="75e6f-277">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="75e6f-278">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="75e6f-279">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="75e6f-279">In the following code:</span></span>

* <span data-ttu-id="75e6f-280">As políticas "regular" e "longa" são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="75e6f-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> Adiciona as políticas "regular" e "longa" do registro.</span><span class="sxs-lookup"><span data-stu-id="75e6f-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="75e6f-282">Para obter mais informações sobre as `IHttpClientFactory` integrações do e do Polly, consulte o [wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="75e6f-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="75e6f-283">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="75e6f-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="75e6f-284">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="75e6f-285">Um <xref:System.Net.Http.HttpMessageHandler> é criado por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="75e6f-286">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="75e6f-287">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="75e6f-288">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="75e6f-289">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="75e6f-290">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="75e6f-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="75e6f-291">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reajam às alterações de DNS (sistema de nomes de domínio).</span><span class="sxs-lookup"><span data-stu-id="75e6f-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="75e6f-292">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="75e6f-293">O valor padrão pode ser substituído em uma base de cliente por nome:</span><span class="sxs-lookup"><span data-stu-id="75e6f-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="75e6f-294">`HttpClient` as instâncias geralmente podem ser tratadas como objetos .NET que **não** exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="75e6f-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="75e6f-295">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="75e6f-296">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="75e6f-297">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="75e6f-298">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="75e6f-299">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="75e6f-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="75e6f-300">`IHttpClientFactory`O uso do em um aplicativo habilitado para di evita:</span><span class="sxs-lookup"><span data-stu-id="75e6f-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="75e6f-301">Problemas de esgotamento de recursos por instâncias de Pooling `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="75e6f-302">Problemas de DNS obsoletos por `HttpMessageHandler` instâncias de ciclo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="75e6f-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="75e6f-303">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de vida longa <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="75e6f-304">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="75e6f-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="75e6f-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="75e6f-306">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="75e6f-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="75e6f-307">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que são `IHttpClientFactory` resolvidos de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="75e6f-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="75e6f-308">O `SocketsHttpHandler` compartilha conexões entre `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="75e6f-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-309">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="75e6f-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="75e6f-310">O `SocketsHttpHandler` ciclos se conexões de acordo com o `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="75e6f-311">Cookies</span><span class="sxs-lookup"><span data-stu-id="75e6f-311">Cookies</span></span>

<span data-ttu-id="75e6f-312">As `HttpMessageHandler` instâncias em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="75e6f-313">O `CookieContainer` compartilhamento de objeto inesperado geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="75e6f-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="75e6f-314">Para aplicativos que exigem cookie s, considere o:</span><span class="sxs-lookup"><span data-stu-id="75e6f-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="75e6f-315">Desabilitando a cookie manipulação automática</span><span class="sxs-lookup"><span data-stu-id="75e6f-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="75e6f-316">Evitá `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="75e6f-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="75e6f-317">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar a cookie manipulação automática:</span><span class="sxs-lookup"><span data-stu-id="75e6f-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="75e6f-318">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="75e6f-318">Logging</span></span>

<span data-ttu-id="75e6f-319">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="75e6f-320">Habilite o nível de informações apropriado na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="75e6f-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="75e6f-321">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="75e6f-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="75e6f-322">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="75e6f-323">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de "System .net. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="75e6f-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="75e6f-324">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="75e6f-325">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="75e6f-326">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="75e6f-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="75e6f-327">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="75e6f-328">No exemplo de *MyNamedClient* , essas mensagens são registradas com a categoria de log "System .net. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="75e6f-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="75e6f-329">Para a solicitação, isso ocorre depois que todos os outros manipuladores forem executados e imediatamente antes de a solicitação ser enviada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="75e6f-330">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="75e6f-331">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="75e6f-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="75e6f-332">Isso pode incluir alterações nos cabeçalhos de solicitação ou no código de status de resposta.</span><span class="sxs-lookup"><span data-stu-id="75e6f-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="75e6f-333">A inclusão do nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="75e6f-334">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="75e6f-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="75e6f-335">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="75e6f-336">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="75e6f-337">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="75e6f-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="75e6f-338">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="75e6f-339">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="75e6f-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="75e6f-340">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="75e6f-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="75e6f-341">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="75e6f-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="75e6f-342">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="75e6f-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="75e6f-343">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="75e6f-343">In the following example:</span></span>

* <span data-ttu-id="75e6f-344"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="75e6f-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="75e6f-345">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="75e6f-346">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="75e6f-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="75e6f-347">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="75e6f-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="75e6f-348">Middleware de propagação de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="75e6f-348">Header propagation middleware</span></span>

<span data-ttu-id="75e6f-349">A propagação de cabeçalho é um middleware ASP.NET Core para propagar cabeçalhos HTTP da solicitação de entrada para as solicitações de cliente HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="75e6f-350">Para usar a propagação de cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="75e6f-350">To use header propagation:</span></span>

* <span data-ttu-id="75e6f-351">Referencie o pacote [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="75e6f-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="75e6f-352">Configure o middleware e `HttpClient` em `Startup` :</span><span class="sxs-lookup"><span data-stu-id="75e6f-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="75e6f-353">O cliente inclui os cabeçalhos configurados nas solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="75e6f-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="75e6f-354">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="75e6f-354">Additional resources</span></span>

* [<span data-ttu-id="75e6f-355">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="75e6f-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="75e6f-356">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="75e6f-357">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="75e6f-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="75e6f-358">Como serializar e desserializar JSON no .NET</span><span class="sxs-lookup"><span data-stu-id="75e6f-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="75e6f-359">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="75e6f-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="75e6f-360">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="75e6f-361">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="75e6f-361">It offers the following benefits:</span></span>

* <span data-ttu-id="75e6f-362">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-363">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="75e6f-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="75e6f-364">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="75e6f-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="75e6f-365">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="75e6f-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="75e6f-366">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="75e6f-367">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="75e6f-368">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="75e6f-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="75e6f-369">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="75e6f-369">Consumption patterns</span></span>

<span data-ttu-id="75e6f-370">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="75e6f-371">Uso básico</span><span class="sxs-lookup"><span data-stu-id="75e6f-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="75e6f-372">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="75e6f-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="75e6f-373">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="75e6f-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="75e6f-374">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="75e6f-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="75e6f-375">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="75e6f-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="75e6f-376">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="75e6f-377">Uso básico</span><span class="sxs-lookup"><span data-stu-id="75e6f-377">Basic usage</span></span>

<span data-ttu-id="75e6f-378">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="75e6f-379">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="75e6f-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="75e6f-380">O `IHttpClientFactory` pode ser usado para criar uma `HttpClient` instância do:</span><span class="sxs-lookup"><span data-stu-id="75e6f-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="75e6f-381">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="75e6f-382">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="75e6f-383">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="75e6f-384">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="75e6f-384">Named clients</span></span>

<span data-ttu-id="75e6f-385">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="75e6f-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="75e6f-386">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="75e6f-387">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="75e6f-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="75e6f-388">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="75e6f-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="75e6f-389">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="75e6f-390">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="75e6f-391">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="75e6f-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="75e6f-392">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="75e6f-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="75e6f-393">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="75e6f-394">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="75e6f-394">Typed clients</span></span>

<span data-ttu-id="75e6f-395">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-395">Typed clients:</span></span>

* <span data-ttu-id="75e6f-396">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="75e6f-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="75e6f-397">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="75e6f-398">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="75e6f-399">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="75e6f-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="75e6f-400">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="75e6f-401">Um cliente tipado aceita um `HttpClient` parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="75e6f-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="75e6f-402">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="75e6f-403">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="75e6f-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="75e6f-404">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="75e6f-405">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="75e6f-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="75e6f-406">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="75e6f-407">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="75e6f-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="75e6f-408">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="75e6f-409">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="75e6f-410">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="75e6f-411">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="75e6f-412">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="75e6f-413">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="75e6f-414">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="75e6f-414">Generated clients</span></span>

<span data-ttu-id="75e6f-415">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="75e6f-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="75e6f-416">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="75e6f-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="75e6f-417">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="75e6f-418">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="75e6f-419">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="75e6f-419">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="75e6f-420">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="75e6f-420">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="75e6f-421">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="75e6f-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="75e6f-422">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="75e6f-422">Outgoing request middleware</span></span>

<span data-ttu-id="75e6f-423">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="75e6f-424">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="75e6f-425">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="75e6f-426">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="75e6f-427">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75e6f-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="75e6f-428">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="75e6f-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="75e6f-429">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="75e6f-430">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="75e6f-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="75e6f-431">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="75e6f-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="75e6f-432">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="75e6f-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="75e6f-433">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="75e6f-434">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração de um `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="75e6f-435">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="75e6f-436">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="75e6f-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="75e6f-437">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="75e6f-438">Os manipuladores podem depender de serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="75e6f-439">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="75e6f-440">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="75e6f-441">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="75e6f-442">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="75e6f-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="75e6f-443">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="75e6f-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="75e6f-444">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="75e6f-445">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="75e6f-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="75e6f-446">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="75e6f-447">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-447">Use Polly-based handlers</span></span>

<span data-ttu-id="75e6f-448">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="75e6f-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="75e6f-449">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="75e6f-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="75e6f-450">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="75e6f-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="75e6f-451">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-452">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="75e6f-452">The Polly extensions:</span></span>

* <span data-ttu-id="75e6f-453">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="75e6f-454">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="75e6f-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="75e6f-455">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="75e6f-456">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="75e6f-456">Handle transient faults</span></span>

<span data-ttu-id="75e6f-457">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="75e6f-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="75e6f-458">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="75e6f-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="75e6f-459">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="75e6f-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="75e6f-460">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="75e6f-461">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="75e6f-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="75e6f-462">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="75e6f-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="75e6f-463">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="75e6f-464">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="75e6f-464">Dynamically select policies</span></span>

<span data-ttu-id="75e6f-465">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="75e6f-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="75e6f-466">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="75e6f-467">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="75e6f-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="75e6f-468">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="75e6f-469">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="75e6f-470">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="75e6f-471">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="75e6f-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="75e6f-472">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="75e6f-473">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="75e6f-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="75e6f-474">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="75e6f-475">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="75e6f-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="75e6f-476">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="75e6f-477">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="75e6f-478">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="75e6f-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="75e6f-479">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="75e6f-480">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="75e6f-481">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="75e6f-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="75e6f-482">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="75e6f-483">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="75e6f-484">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="75e6f-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="75e6f-485">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="75e6f-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="75e6f-486">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="75e6f-487">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="75e6f-488">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="75e6f-489">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="75e6f-490">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="75e6f-491">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="75e6f-492">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="75e6f-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="75e6f-493">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="75e6f-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="75e6f-494">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="75e6f-495">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="75e6f-496">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="75e6f-497">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="75e6f-498">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="75e6f-499">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-500">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="75e6f-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="75e6f-501">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="75e6f-502">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="75e6f-503">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="75e6f-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="75e6f-504">`IHttpClientFactory`O uso do em um aplicativo habilitado para di evita:</span><span class="sxs-lookup"><span data-stu-id="75e6f-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="75e6f-505">Problemas de esgotamento de recursos por instâncias de Pooling `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="75e6f-506">Problemas de DNS obsoletos por `HttpMessageHandler` instâncias de ciclo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="75e6f-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="75e6f-507">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de vida longa <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="75e6f-508">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="75e6f-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="75e6f-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="75e6f-510">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="75e6f-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="75e6f-511">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que são `IHttpClientFactory` resolvidos de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="75e6f-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="75e6f-512">O `SocketsHttpHandler` compartilha conexões entre `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="75e6f-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-513">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="75e6f-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="75e6f-514">O `SocketsHttpHandler` ciclos se conexões de acordo com o `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="75e6f-515">Cookies</span><span class="sxs-lookup"><span data-stu-id="75e6f-515">Cookies</span></span>

<span data-ttu-id="75e6f-516">As `HttpMessageHandler` instâncias em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="75e6f-517">O `CookieContainer` compartilhamento de objeto inesperado geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="75e6f-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="75e6f-518">Para aplicativos que exigem cookie s, considere o:</span><span class="sxs-lookup"><span data-stu-id="75e6f-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="75e6f-519">Desabilitando a cookie manipulação automática</span><span class="sxs-lookup"><span data-stu-id="75e6f-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="75e6f-520">Evitá `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="75e6f-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="75e6f-521">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar a cookie manipulação automática:</span><span class="sxs-lookup"><span data-stu-id="75e6f-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="75e6f-522">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="75e6f-522">Logging</span></span>

<span data-ttu-id="75e6f-523">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="75e6f-524">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="75e6f-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="75e6f-525">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="75e6f-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="75e6f-526">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="75e6f-527">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="75e6f-528">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="75e6f-529">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="75e6f-530">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="75e6f-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="75e6f-531">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="75e6f-532">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="75e6f-533">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="75e6f-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="75e6f-534">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="75e6f-535">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="75e6f-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="75e6f-536">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="75e6f-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="75e6f-537">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="75e6f-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="75e6f-538">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="75e6f-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="75e6f-539">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="75e6f-540">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="75e6f-541">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="75e6f-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="75e6f-542">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="75e6f-543">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="75e6f-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="75e6f-544">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="75e6f-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="75e6f-545">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="75e6f-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="75e6f-546">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="75e6f-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="75e6f-547">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="75e6f-547">In the following example:</span></span>

* <span data-ttu-id="75e6f-548"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="75e6f-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="75e6f-549">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="75e6f-550">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="75e6f-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="75e6f-551">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="75e6f-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="75e6f-552">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="75e6f-552">Additional resources</span></span>

* [<span data-ttu-id="75e6f-553">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="75e6f-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="75e6f-554">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="75e6f-555">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="75e6f-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="75e6f-556">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="75e6f-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="75e6f-557">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="75e6f-558">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="75e6f-558">It offers the following benefits:</span></span>

* <span data-ttu-id="75e6f-559">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-560">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="75e6f-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="75e6f-561">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="75e6f-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="75e6f-562">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="75e6f-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="75e6f-563">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="75e6f-564">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="75e6f-565">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="75e6f-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="75e6f-566">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="75e6f-566">Prerequisites</span></span>

<span data-ttu-id="75e6f-567">Os projetos direcionados ao .NET Framework exigem a instalação do pacote do NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="75e6f-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="75e6f-568">Os projetos destinados ao .Net Core e a referência ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) já incluem o pacote `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="75e6f-569">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="75e6f-569">Consumption patterns</span></span>

<span data-ttu-id="75e6f-570">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="75e6f-571">Uso básico</span><span class="sxs-lookup"><span data-stu-id="75e6f-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="75e6f-572">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="75e6f-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="75e6f-573">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="75e6f-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="75e6f-574">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="75e6f-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="75e6f-575">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="75e6f-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="75e6f-576">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="75e6f-577">Uso básico</span><span class="sxs-lookup"><span data-stu-id="75e6f-577">Basic usage</span></span>

<span data-ttu-id="75e6f-578">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="75e6f-579">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="75e6f-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="75e6f-580">O `IHttpClientFactory` pode ser usado para criar uma `HttpClient` instância do:</span><span class="sxs-lookup"><span data-stu-id="75e6f-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="75e6f-581">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="75e6f-582">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="75e6f-583">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="75e6f-584">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="75e6f-584">Named clients</span></span>

<span data-ttu-id="75e6f-585">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="75e6f-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="75e6f-586">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="75e6f-587">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="75e6f-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="75e6f-588">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="75e6f-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="75e6f-589">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="75e6f-590">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="75e6f-591">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="75e6f-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="75e6f-592">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="75e6f-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="75e6f-593">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="75e6f-594">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="75e6f-594">Typed clients</span></span>

<span data-ttu-id="75e6f-595">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-595">Typed clients:</span></span>

* <span data-ttu-id="75e6f-596">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="75e6f-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="75e6f-597">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="75e6f-598">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="75e6f-599">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="75e6f-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="75e6f-600">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="75e6f-601">Um cliente tipado aceita um `HttpClient` parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="75e6f-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="75e6f-602">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="75e6f-603">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="75e6f-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="75e6f-604">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="75e6f-605">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="75e6f-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="75e6f-606">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="75e6f-607">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="75e6f-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="75e6f-608">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="75e6f-609">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="75e6f-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="75e6f-610">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="75e6f-611">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="75e6f-612">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="75e6f-613">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="75e6f-614">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="75e6f-614">Generated clients</span></span>

<span data-ttu-id="75e6f-615">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="75e6f-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="75e6f-616">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="75e6f-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="75e6f-617">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="75e6f-618">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="75e6f-619">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="75e6f-619">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="75e6f-620">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="75e6f-620">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="75e6f-621">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="75e6f-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="75e6f-622">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="75e6f-622">Outgoing request middleware</span></span>

<span data-ttu-id="75e6f-623">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="75e6f-624">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="75e6f-625">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="75e6f-626">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="75e6f-627">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75e6f-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="75e6f-628">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="75e6f-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="75e6f-629">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="75e6f-630">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="75e6f-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="75e6f-631">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="75e6f-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="75e6f-632">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="75e6f-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="75e6f-633">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="75e6f-634">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração de um `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="75e6f-635">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="75e6f-636">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="75e6f-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="75e6f-637">O manipulador **deve** ser registrado na injeção de dependência como serviço temporário, mas nunca com escopo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="75e6f-638">Se o manipulador estiver registrado como um serviço com escopo e algum serviço do qual ele depende for descartável:</span><span class="sxs-lookup"><span data-stu-id="75e6f-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="75e6f-639">Os serviços do manipulador poderão ser descartados antes que ele saia do escopo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="75e6f-640">Os serviços de manipulador descartados farão com que o manipulador falhe.</span><span class="sxs-lookup"><span data-stu-id="75e6f-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="75e6f-641">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando no tipo do manipulador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="75e6f-642">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="75e6f-643">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="75e6f-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="75e6f-644">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="75e6f-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="75e6f-645">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="75e6f-646">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="75e6f-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="75e6f-647">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="75e6f-648">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-648">Use Polly-based handlers</span></span>

<span data-ttu-id="75e6f-649">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="75e6f-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="75e6f-650">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="75e6f-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="75e6f-651">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="75e6f-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="75e6f-652">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-653">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="75e6f-653">The Polly extensions:</span></span>

* <span data-ttu-id="75e6f-654">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="75e6f-655">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="75e6f-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="75e6f-656">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="75e6f-657">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="75e6f-657">Handle transient faults</span></span>

<span data-ttu-id="75e6f-658">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="75e6f-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="75e6f-659">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="75e6f-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="75e6f-660">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="75e6f-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="75e6f-661">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="75e6f-662">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="75e6f-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="75e6f-663">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="75e6f-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="75e6f-664">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="75e6f-665">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="75e6f-665">Dynamically select policies</span></span>

<span data-ttu-id="75e6f-666">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="75e6f-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="75e6f-667">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="75e6f-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="75e6f-668">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="75e6f-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="75e6f-669">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="75e6f-670">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="75e6f-671">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="75e6f-672">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="75e6f-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="75e6f-673">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="75e6f-674">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="75e6f-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="75e6f-675">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="75e6f-676">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="75e6f-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="75e6f-677">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="75e6f-678">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="75e6f-679">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="75e6f-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="75e6f-680">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="75e6f-681">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="75e6f-682">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="75e6f-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="75e6f-683">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="75e6f-684">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="75e6f-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="75e6f-685">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="75e6f-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="75e6f-686">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="75e6f-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="75e6f-687">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="75e6f-688">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="75e6f-689">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="75e6f-690">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="75e6f-691">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="75e6f-692">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="75e6f-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="75e6f-693">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="75e6f-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="75e6f-694">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="75e6f-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="75e6f-695">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="75e6f-696">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="75e6f-697">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="75e6f-698">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="75e6f-699">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="75e6f-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="75e6f-700">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-701">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="75e6f-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="75e6f-702">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="75e6f-703">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="75e6f-704">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="75e6f-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="75e6f-705">`IHttpClientFactory`O uso do em um aplicativo habilitado para di evita:</span><span class="sxs-lookup"><span data-stu-id="75e6f-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="75e6f-706">Problemas de esgotamento de recursos por instâncias de Pooling `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="75e6f-707">Problemas de DNS obsoletos por `HttpMessageHandler` instâncias de ciclo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="75e6f-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="75e6f-708">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de vida longa <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="75e6f-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="75e6f-709">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="75e6f-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="75e6f-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="75e6f-711">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="75e6f-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="75e6f-712">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que são `IHttpClientFactory` resolvidos de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="75e6f-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="75e6f-713">O `SocketsHttpHandler` compartilha conexões entre `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="75e6f-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="75e6f-714">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="75e6f-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="75e6f-715">O `SocketsHttpHandler` ciclos se conexões de acordo com o `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="75e6f-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="75e6f-716">Cookies</span><span class="sxs-lookup"><span data-stu-id="75e6f-716">Cookies</span></span>

<span data-ttu-id="75e6f-717">As `HttpMessageHandler` instâncias em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="75e6f-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="75e6f-718">O `CookieContainer` compartilhamento de objeto inesperado geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="75e6f-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="75e6f-719">Para aplicativos que exigem cookie s, considere o:</span><span class="sxs-lookup"><span data-stu-id="75e6f-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="75e6f-720">Desabilitando a cookie manipulação automática</span><span class="sxs-lookup"><span data-stu-id="75e6f-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="75e6f-721">Evitá `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="75e6f-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="75e6f-722">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar a cookie manipulação automática:</span><span class="sxs-lookup"><span data-stu-id="75e6f-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="75e6f-723">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="75e6f-723">Logging</span></span>

<span data-ttu-id="75e6f-724">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="75e6f-725">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="75e6f-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="75e6f-726">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="75e6f-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="75e6f-727">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="75e6f-728">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="75e6f-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="75e6f-729">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="75e6f-730">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="75e6f-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="75e6f-731">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="75e6f-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="75e6f-732">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="75e6f-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="75e6f-733">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="75e6f-734">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="75e6f-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="75e6f-735">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="75e6f-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="75e6f-736">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="75e6f-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="75e6f-737">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="75e6f-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="75e6f-738">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="75e6f-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="75e6f-739">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="75e6f-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="75e6f-740">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="75e6f-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="75e6f-741">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="75e6f-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="75e6f-742">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="75e6f-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="75e6f-743">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="75e6f-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="75e6f-744">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="75e6f-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="75e6f-745">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="75e6f-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="75e6f-746">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="75e6f-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="75e6f-747">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="75e6f-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="75e6f-748">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="75e6f-748">In the following example:</span></span>

* <span data-ttu-id="75e6f-749"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="75e6f-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="75e6f-750">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="75e6f-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="75e6f-751">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="75e6f-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="75e6f-752">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="75e6f-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="75e6f-753">Middleware de propagação de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="75e6f-753">Header propagation middleware</span></span>

<span data-ttu-id="75e6f-754">A propagação de cabeçalho é um middleware com suporte da Comunidade para propagar cabeçalhos HTTP da solicitação de entrada para as solicitações de cliente HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="75e6f-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="75e6f-755">Para usar a propagação de cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="75e6f-755">To use header propagation:</span></span>

* <span data-ttu-id="75e6f-756">Referencie a porta com suporte da Comunidade do pacote [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="75e6f-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="75e6f-757">ASP.NET Core 3,1 e posterior dá suporte a [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="75e6f-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="75e6f-758">Configure o middleware e `HttpClient` em `Startup` :</span><span class="sxs-lookup"><span data-stu-id="75e6f-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="75e6f-759">O cliente inclui os cabeçalhos configurados nas solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="75e6f-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="75e6f-760">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="75e6f-760">Additional resources</span></span>

* [<span data-ttu-id="75e6f-761">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="75e6f-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="75e6f-762">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="75e6f-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="75e6f-763">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="75e6f-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
