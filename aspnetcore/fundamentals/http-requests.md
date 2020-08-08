---
title: Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core
author: stevejgordon
description: Saiba mais sobre como usar a interface IHttpClientFactory para gerenciar instâncias de HttpClient lógicas no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
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
ms.openlocfilehash: ef6ed0911c2615ea543b1c2cea31a9a453f0c604
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017227"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="da4ed-103">Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da4ed-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="da4ed-104">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="da4ed-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="da4ed-105">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="da4ed-106">`IHttpClientFactory`oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="da4ed-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="da4ed-107">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-108">Por exemplo, um cliente chamado *GitHub* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="da4ed-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="da4ed-109">Um cliente padrão pode ser registrado para acesso geral.</span><span class="sxs-lookup"><span data-stu-id="da4ed-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="da4ed-110">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="da4ed-111">Fornece extensões para o middleware baseado em Polly para tirar proveito da delegação de manipuladores no `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="da4ed-112">Gerencia o pooling e o tempo de vida de instâncias subjacentes `HttpClientMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="da4ed-113">O gerenciamento automático evita problemas comuns de DNS (sistema de nomes de domínio) que ocorrem ao gerenciar manualmente os `HttpClient` tempos de vida.</span><span class="sxs-lookup"><span data-stu-id="da4ed-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="da4ed-114">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="da4ed-115">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="da4ed-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="da4ed-116">O código de exemplo nesta versão do tópico usa <xref:System.Text.Json> para desserializar o conteúdo JSON retornado em respostas http.</span><span class="sxs-lookup"><span data-stu-id="da4ed-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="da4ed-117">Para obter exemplos que usam `Json.NET` e `ReadAsAsync<T>` , use o seletor de versão para selecionar uma versão 2. x deste tópico.</span><span class="sxs-lookup"><span data-stu-id="da4ed-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="da4ed-118">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="da4ed-118">Consumption patterns</span></span>

<span data-ttu-id="da4ed-119">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="da4ed-120">Uso básico</span><span class="sxs-lookup"><span data-stu-id="da4ed-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="da4ed-121">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="da4ed-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="da4ed-122">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="da4ed-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="da4ed-123">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="da4ed-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="da4ed-124">A melhor abordagem depende dos requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="da4ed-125">Uso básico</span><span class="sxs-lookup"><span data-stu-id="da4ed-125">Basic usage</span></span>

<span data-ttu-id="da4ed-126">`IHttpClientFactory`pode ser registrado chamando `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="da4ed-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="da4ed-127">Um `IHttpClientFactory` pode ser solicitado usando [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da4ed-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="da4ed-128">O código a seguir usa `IHttpClientFactory` para criar uma `HttpClient` instância do:</span><span class="sxs-lookup"><span data-stu-id="da4ed-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="da4ed-129">Usar `IHttpClientFactory` como no exemplo anterior é uma boa maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="da4ed-130">Ele não tem impacto sobre como o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="da4ed-131">Em locais onde `HttpClient` as instâncias são criadas em um aplicativo existente, substitua essas ocorrências por chamadas para <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="da4ed-132">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="da4ed-132">Named clients</span></span>

<span data-ttu-id="da4ed-133">Os clientes nomeados são uma boa opção quando:</span><span class="sxs-lookup"><span data-stu-id="da4ed-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="da4ed-134">O aplicativo requer muitos usos distintos do `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="da4ed-135">Muitos `HttpClient` s têm configuração diferente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="da4ed-136">A configuração de um nome `HttpClient` pode ser especificada durante o registro em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="da4ed-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="da4ed-137">No código anterior, o cliente está configurado com:</span><span class="sxs-lookup"><span data-stu-id="da4ed-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="da4ed-138">O endereço base `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="da4ed-139">Dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="da4ed-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="da4ed-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="da4ed-140">CreateClient</span></span>

<span data-ttu-id="da4ed-141">Cada vez <xref:System.Net.Http.IHttpClientFactory.CreateClient*> é chamado:</span><span class="sxs-lookup"><span data-stu-id="da4ed-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="da4ed-142">Uma nova instância do `HttpClient` é criada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="da4ed-143">A ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-143">The configuration action is called.</span></span>

<span data-ttu-id="da4ed-144">Para criar um cliente nomeado, passe seu nome para `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="da4ed-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="da4ed-145">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="da4ed-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="da4ed-146">O código pode passar apenas o caminho, já que o endereço base configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="da4ed-147">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="da4ed-147">Typed clients</span></span>

<span data-ttu-id="da4ed-148">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-148">Typed clients:</span></span>

* <span data-ttu-id="da4ed-149">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="da4ed-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="da4ed-150">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="da4ed-151">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="da4ed-152">Por exemplo, um único cliente tipado pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="da4ed-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="da4ed-153">Para um ponto de extremidade de back-end único.</span><span class="sxs-lookup"><span data-stu-id="da4ed-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="da4ed-154">Para encapsular toda a lógica lidando com o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="da4ed-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="da4ed-155">Trabalhe com DI e pode ser injetado onde necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="da4ed-156">Um cliente tipado aceita um `HttpClient` parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="da4ed-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="da4ed-157">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="da4ed-157">In the preceding code:</span></span>

* <span data-ttu-id="da4ed-158">A configuração é movida para o cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="da4ed-159">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="da4ed-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="da4ed-160">Métodos específicos de API podem ser criados para expor a `HttpClient` funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="da4ed-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="da4ed-161">Por exemplo, o `GetAspNetDocsIssues` método encapsula o código para recuperar problemas abertos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="da4ed-162">O código a seguir <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> chama `Startup.ConfigureServices` para registrar uma classe de cliente tipada:</span><span class="sxs-lookup"><span data-stu-id="da4ed-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="da4ed-163">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="da4ed-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="da4ed-164">No código anterior, o `AddHttpClient` registra `GitHubService` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="da4ed-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="da4ed-165">Esse registro usa um método de fábrica para:</span><span class="sxs-lookup"><span data-stu-id="da4ed-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="da4ed-166">Crie uma instância de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="da4ed-167">Crie uma instância do `GitHubService` , passando a instância do `HttpClient` para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="da4ed-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="da4ed-168">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="da4ed-169">A configuração para um cliente tipado pode ser especificada durante o registro em `Startup.ConfigureServices` , em vez de no construtor do cliente digitado:</span><span class="sxs-lookup"><span data-stu-id="da4ed-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="da4ed-170">O `HttpClient` pode ser encapsulado em um cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="da4ed-171">Em vez de expô-lo como uma propriedade, defina um método que chame a `HttpClient` instância internamente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="da4ed-172">No código anterior, o `HttpClient` é armazenado em um campo particular.</span><span class="sxs-lookup"><span data-stu-id="da4ed-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="da4ed-173">O acesso ao `HttpClient` é pelo método público `GetRepos` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="da4ed-174">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="da4ed-174">Generated clients</span></span>

<span data-ttu-id="da4ed-175">`IHttpClientFactory`pode ser usado em combinação com bibliotecas de terceiros, como [rEFIt](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="da4ed-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="da4ed-176">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="da4ed-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="da4ed-177">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="da4ed-178">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="da4ed-179">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="da4ed-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="da4ed-180">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="da4ed-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="da4ed-181">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="da4ed-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="da4ed-182">Fazer solicitações POST, PUT e DELETE</span><span class="sxs-lookup"><span data-stu-id="da4ed-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="da4ed-183">Nos exemplos anteriores, todas as solicitações HTTP usam o verbo GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="da4ed-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="da4ed-184">`HttpClient`também dá suporte a outros verbos HTTP, incluindo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="da4ed-185">POST</span><span class="sxs-lookup"><span data-stu-id="da4ed-185">POST</span></span>
* <span data-ttu-id="da4ed-186">PUT</span><span class="sxs-lookup"><span data-stu-id="da4ed-186">PUT</span></span>
* <span data-ttu-id="da4ed-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="da4ed-187">DELETE</span></span>
* <span data-ttu-id="da4ed-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="da4ed-188">PATCH</span></span>

<span data-ttu-id="da4ed-189">Para obter uma lista completa de verbos HTTP com suporte, consulte <xref:System.Net.Http.HttpMethod> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="da4ed-190">O exemplo a seguir mostra como fazer uma solicitação HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="da4ed-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="da4ed-191">No código anterior, o `CreateItemAsync` método:</span><span class="sxs-lookup"><span data-stu-id="da4ed-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="da4ed-192">Serializa o `TodoItem` parâmetro para JSON usando `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="da4ed-193">Isso usa uma instância do <xref:System.Text.Json.JsonSerializerOptions> para configurar o processo de serialização.</span><span class="sxs-lookup"><span data-stu-id="da4ed-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="da4ed-194">Cria uma instância do <xref:System.Net.Http.StringContent> para empacotar o JSON serializado para envio no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="da4ed-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="da4ed-195">Chamadas <xref:System.Net.Http.HttpClient.PostAsync%2A> para enviar o conteúdo JSON para a URL especificada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="da4ed-196">Essa é uma URL relativa que é adicionada ao [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="da4ed-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="da4ed-197">Chamadas <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> para gerar uma exceção se o código de status de resposta não indicar êxito.</span><span class="sxs-lookup"><span data-stu-id="da4ed-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="da4ed-198">`HttpClient`também oferece suporte a outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="da4ed-199">Por exemplo, <xref:System.Net.Http.MultipartContent> e <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="da4ed-200">Para obter uma lista completa de conteúdo com suporte, consulte <xref:System.Net.Http.HttpContent> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="da4ed-201">O exemplo a seguir mostra uma solicitação HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="da4ed-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="da4ed-202">O código anterior é muito semelhante ao exemplo de POST.</span><span class="sxs-lookup"><span data-stu-id="da4ed-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="da4ed-203">O `SaveItemAsync` método chama <xref:System.Net.Http.HttpClient.PutAsync%2A> em vez de `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="da4ed-204">O exemplo a seguir mostra uma solicitação HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="da4ed-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="da4ed-205">No código anterior, o `DeleteItemAsync` método chama <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="da4ed-206">Como as solicitações HTTP DELETE normalmente não contêm corpo, o `DeleteAsync` método não fornece uma sobrecarga que aceita uma instância do `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="da4ed-207">Para saber mais sobre como usar verbos HTTP diferentes com o `HttpClient` , consulte <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="da4ed-208">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="da4ed-208">Outgoing request middleware</span></span>

<span data-ttu-id="da4ed-209">`HttpClient`tem o conceito de delegar manipuladores que podem ser vinculados juntos para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="da4ed-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="da4ed-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="da4ed-211">Simplifica a definição dos manipuladores a serem aplicados para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="da4ed-212">Dá suporte ao registro e encadeamento de vários manipuladores para criar um pipeline de middleware de solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="da4ed-213">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="da4ed-214">Esse padrão:</span><span class="sxs-lookup"><span data-stu-id="da4ed-214">This pattern:</span></span>

  * <span data-ttu-id="da4ed-215">É semelhante ao pipeline de middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="da4ed-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="da4ed-216">Fornece um mecanismo para gerenciar preocupações abrangentes em relação a solicitações HTTP, como:</span><span class="sxs-lookup"><span data-stu-id="da4ed-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="da4ed-217">cache</span><span class="sxs-lookup"><span data-stu-id="da4ed-217">caching</span></span>
    * <span data-ttu-id="da4ed-218">tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="da4ed-218">error handling</span></span>
    * <span data-ttu-id="da4ed-219">serialização</span><span class="sxs-lookup"><span data-stu-id="da4ed-219">serialization</span></span>
    * <span data-ttu-id="da4ed-220">registro em log</span><span class="sxs-lookup"><span data-stu-id="da4ed-220">logging</span></span>

<span data-ttu-id="da4ed-221">Para criar um manipulador de delegação:</span><span class="sxs-lookup"><span data-stu-id="da4ed-221">To create a delegating handler:</span></span>

* <span data-ttu-id="da4ed-222">Derivar de <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="da4ed-223">Substitua <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="da4ed-224">Execute o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="da4ed-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="da4ed-225">O código anterior verifica se o `X-API-KEY` cabeçalho está na solicitação.</span><span class="sxs-lookup"><span data-stu-id="da4ed-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="da4ed-226">Se `X-API-KEY` estiver ausente, <xref:System.Net.HttpStatusCode.BadRequest> será retornado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="da4ed-227">Mais de um manipulador pode ser adicionado à configuração para um `HttpClient` com <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="da4ed-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="da4ed-228">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="da4ed-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="da4ed-229">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="da4ed-230">Os manipuladores podem depender dos serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="da4ed-231">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="da4ed-232">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="da4ed-233">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="da4ed-234">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="da4ed-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="da4ed-235">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="da4ed-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="da4ed-236">Passe dados para o manipulador usando [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="da4ed-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="da4ed-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="da4ed-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="da4ed-238">Crie um objeto de armazenamento <xref:System.Threading.AsyncLocal`1> personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="da4ed-239">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-239">Use Polly-based handlers</span></span>

<span data-ttu-id="da4ed-240">`IHttpClientFactory`integra-se com a biblioteca de terceiros [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="da4ed-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="da4ed-241">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="da4ed-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="da4ed-242">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="da4ed-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="da4ed-243">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-244">As extensões Polly dão suporte à adição de manipuladores baseados em Polly a clientes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="da4ed-245">Polly requer o pacote NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="da4ed-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="da4ed-246">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="da4ed-246">Handle transient faults</span></span>

<span data-ttu-id="da4ed-247">As falhas normalmente ocorrem quando chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="da4ed-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="da4ed-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>permite que uma política seja definida para lidar com erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="da4ed-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="da4ed-249">Políticas configuradas com `AddTransientHttpErrorPolicy` o tratamento das seguintes respostas:</span><span class="sxs-lookup"><span data-stu-id="da4ed-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="da4ed-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="da4ed-250">HTTP 5xx</span></span>
* <span data-ttu-id="da4ed-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="da4ed-251">HTTP 408</span></span>

<span data-ttu-id="da4ed-252">`AddTransientHttpErrorPolicy`fornece acesso a um `PolicyBuilder` objeto configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="da4ed-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="da4ed-253">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="da4ed-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="da4ed-254">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="da4ed-255">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="da4ed-255">Dynamically select policies</span></span>

<span data-ttu-id="da4ed-256">Os métodos de extensão são fornecidos para adicionar manipuladores baseados em Polly, por exemplo, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="da4ed-257">A sobrecarga a seguir `AddPolicyHandler` inspeciona a solicitação para decidir qual política aplicar:</span><span class="sxs-lookup"><span data-stu-id="da4ed-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="da4ed-258">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="da4ed-259">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="da4ed-260">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="da4ed-261">É comum aninhar políticas Polly:</span><span class="sxs-lookup"><span data-stu-id="da4ed-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="da4ed-262">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="da4ed-262">In the preceding example:</span></span>

* <span data-ttu-id="da4ed-263">Dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-263">Two handlers are added.</span></span>
* <span data-ttu-id="da4ed-264">O primeiro manipulador usa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="da4ed-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="da4ed-265">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="da4ed-266">A segunda `AddTransientHttpErrorPolicy` chamada adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="da4ed-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="da4ed-267">Outras solicitações externas serão bloqueadas por 30 segundos se 5 tentativas com falha ocorrerem em sequência.</span><span class="sxs-lookup"><span data-stu-id="da4ed-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="da4ed-268">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="da4ed-269">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="da4ed-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="da4ed-270">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="da4ed-271">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="da4ed-272">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="da4ed-272">In the following code:</span></span>

* <span data-ttu-id="da4ed-273">As políticas "regular" e "longa" são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="da4ed-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>Adiciona as políticas "regular" e "longa" do registro.</span><span class="sxs-lookup"><span data-stu-id="da4ed-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="da4ed-275">Para obter mais informações sobre as `IHttpClientFactory` integrações do e do Polly, consulte o [wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="da4ed-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="da4ed-276">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="da4ed-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="da4ed-277">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="da4ed-278">Um <xref:System.Net.Http.HttpMessageHandler> é criado por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="da4ed-279">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="da4ed-280">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="da4ed-281">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="da4ed-282">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="da4ed-283">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="da4ed-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="da4ed-284">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reajam às alterações de DNS (sistema de nomes de domínio).</span><span class="sxs-lookup"><span data-stu-id="da4ed-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="da4ed-285">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="da4ed-286">O valor padrão pode ser substituído em uma base de cliente por nome:</span><span class="sxs-lookup"><span data-stu-id="da4ed-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="da4ed-287">`HttpClient`as instâncias geralmente podem ser tratadas como objetos .NET que **não** exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="da4ed-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="da4ed-288">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="da4ed-289">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="da4ed-290">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="da4ed-291">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="da4ed-292">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="da4ed-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="da4ed-293">`IHttpClientFactory`O uso do em um aplicativo habilitado para di evita:</span><span class="sxs-lookup"><span data-stu-id="da4ed-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="da4ed-294">Problemas de esgotamento de recursos por instâncias de Pooling `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="da4ed-295">Problemas de DNS obsoletos por `HttpMessageHandler` instâncias de ciclo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="da4ed-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="da4ed-296">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de vida longa <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="da4ed-297">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="da4ed-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="da4ed-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="da4ed-299">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="da4ed-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="da4ed-300">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que são `IHttpClientFactory` resolvidos de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="da4ed-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="da4ed-301">O `SocketsHttpHandler` compartilha conexões entre `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="da4ed-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-302">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="da4ed-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="da4ed-303">O `SocketsHttpHandler` ciclos se conexões de acordo com o `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="da4ed-304">Cookies</span><span class="sxs-lookup"><span data-stu-id="da4ed-304">Cookies</span></span>

<span data-ttu-id="da4ed-305">As `HttpMessageHandler` instâncias em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="da4ed-306">O `CookieContainer` compartilhamento de objeto inesperado geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="da4ed-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="da4ed-307">Para aplicativos que exigem cookie s, considere o:</span><span class="sxs-lookup"><span data-stu-id="da4ed-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="da4ed-308">Desabilitando a cookie manipulação automática</span><span class="sxs-lookup"><span data-stu-id="da4ed-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="da4ed-309">Evitá`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="da4ed-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="da4ed-310">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar a cookie manipulação automática:</span><span class="sxs-lookup"><span data-stu-id="da4ed-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="da4ed-311">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="da4ed-311">Logging</span></span>

<span data-ttu-id="da4ed-312">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="da4ed-313">Habilite o nível de informações apropriado na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="da4ed-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="da4ed-314">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="da4ed-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="da4ed-315">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="da4ed-316">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de "System .net. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="da4ed-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="da4ed-317">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="da4ed-318">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="da4ed-319">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="da4ed-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="da4ed-320">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="da4ed-321">No exemplo de *MyNamedClient* , essas mensagens são registradas com a categoria de log "System .net. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="da4ed-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="da4ed-322">Para a solicitação, isso ocorre depois que todos os outros manipuladores forem executados e imediatamente antes de a solicitação ser enviada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="da4ed-323">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="da4ed-324">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="da4ed-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="da4ed-325">Isso pode incluir alterações nos cabeçalhos de solicitação ou no código de status de resposta.</span><span class="sxs-lookup"><span data-stu-id="da4ed-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="da4ed-326">A inclusão do nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="da4ed-327">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="da4ed-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="da4ed-328">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="da4ed-329">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="da4ed-330">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="da4ed-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="da4ed-331">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="da4ed-332">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="da4ed-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="da4ed-333">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="da4ed-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="da4ed-334">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="da4ed-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="da4ed-335">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="da4ed-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="da4ed-336">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="da4ed-336">In the following example:</span></span>

* <span data-ttu-id="da4ed-337"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="da4ed-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="da4ed-338">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="da4ed-339">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="da4ed-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="da4ed-340">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="da4ed-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="da4ed-341">Middleware de propagação de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="da4ed-341">Header propagation middleware</span></span>

<span data-ttu-id="da4ed-342">A propagação de cabeçalho é um middleware ASP.NET Core para propagar cabeçalhos HTTP da solicitação de entrada para as solicitações de cliente HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="da4ed-343">Para usar a propagação de cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="da4ed-343">To use header propagation:</span></span>

* <span data-ttu-id="da4ed-344">Referencie o pacote [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="da4ed-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="da4ed-345">Configure o middleware e `HttpClient` em `Startup` :</span><span class="sxs-lookup"><span data-stu-id="da4ed-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="da4ed-346">O cliente inclui os cabeçalhos configurados nas solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="da4ed-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="da4ed-347">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="da4ed-347">Additional resources</span></span>

* [<span data-ttu-id="da4ed-348">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="da4ed-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="da4ed-349">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="da4ed-350">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="da4ed-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="da4ed-351">Como serializar e desserializar JSON no .NET</span><span class="sxs-lookup"><span data-stu-id="da4ed-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="da4ed-352">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="da4ed-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="da4ed-353">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="da4ed-354">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="da4ed-354">It offers the following benefits:</span></span>

* <span data-ttu-id="da4ed-355">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-356">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="da4ed-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="da4ed-357">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="da4ed-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="da4ed-358">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="da4ed-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="da4ed-359">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="da4ed-360">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="da4ed-361">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da4ed-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="da4ed-362">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="da4ed-362">Consumption patterns</span></span>

<span data-ttu-id="da4ed-363">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="da4ed-364">Uso básico</span><span class="sxs-lookup"><span data-stu-id="da4ed-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="da4ed-365">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="da4ed-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="da4ed-366">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="da4ed-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="da4ed-367">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="da4ed-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="da4ed-368">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="da4ed-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="da4ed-369">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="da4ed-370">Uso básico</span><span class="sxs-lookup"><span data-stu-id="da4ed-370">Basic usage</span></span>

<span data-ttu-id="da4ed-371">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="da4ed-372">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da4ed-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="da4ed-373">O `IHttpClientFactory` pode ser usado para criar uma `HttpClient` instância do:</span><span class="sxs-lookup"><span data-stu-id="da4ed-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="da4ed-374">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="da4ed-375">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="da4ed-376">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="da4ed-377">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="da4ed-377">Named clients</span></span>

<span data-ttu-id="da4ed-378">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="da4ed-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="da4ed-379">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="da4ed-380">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="da4ed-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="da4ed-381">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="da4ed-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="da4ed-382">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="da4ed-383">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="da4ed-384">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="da4ed-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="da4ed-385">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="da4ed-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="da4ed-386">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="da4ed-387">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="da4ed-387">Typed clients</span></span>

<span data-ttu-id="da4ed-388">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-388">Typed clients:</span></span>

* <span data-ttu-id="da4ed-389">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="da4ed-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="da4ed-390">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="da4ed-391">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="da4ed-392">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="da4ed-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="da4ed-393">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="da4ed-394">Um cliente tipado aceita um `HttpClient` parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="da4ed-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="da4ed-395">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="da4ed-396">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="da4ed-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="da4ed-397">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="da4ed-398">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="da4ed-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="da4ed-399">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="da4ed-400">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="da4ed-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="da4ed-401">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="da4ed-402">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="da4ed-403">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="da4ed-404">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="da4ed-405">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="da4ed-406">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="da4ed-407">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="da4ed-407">Generated clients</span></span>

<span data-ttu-id="da4ed-408">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="da4ed-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="da4ed-409">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="da4ed-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="da4ed-410">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="da4ed-411">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="da4ed-412">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="da4ed-412">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="da4ed-413">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="da4ed-413">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="da4ed-414">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="da4ed-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="da4ed-415">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="da4ed-415">Outgoing request middleware</span></span>

<span data-ttu-id="da4ed-416">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="da4ed-417">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="da4ed-418">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="da4ed-419">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="da4ed-420">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="da4ed-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="da4ed-421">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="da4ed-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="da4ed-422">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="da4ed-423">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="da4ed-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="da4ed-424">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="da4ed-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="da4ed-425">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="da4ed-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="da4ed-426">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="da4ed-427">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração de um `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="da4ed-428">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="da4ed-429">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="da4ed-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="da4ed-430">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="da4ed-431">Os manipuladores podem depender de serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="da4ed-432">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="da4ed-433">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="da4ed-434">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="da4ed-435">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="da4ed-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="da4ed-436">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="da4ed-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="da4ed-437">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="da4ed-438">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="da4ed-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="da4ed-439">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="da4ed-440">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-440">Use Polly-based handlers</span></span>

<span data-ttu-id="da4ed-441">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="da4ed-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="da4ed-442">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="da4ed-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="da4ed-443">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="da4ed-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="da4ed-444">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-445">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="da4ed-445">The Polly extensions:</span></span>

* <span data-ttu-id="da4ed-446">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="da4ed-447">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="da4ed-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="da4ed-448">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="da4ed-449">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="da4ed-449">Handle transient faults</span></span>

<span data-ttu-id="da4ed-450">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="da4ed-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="da4ed-451">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="da4ed-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="da4ed-452">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="da4ed-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="da4ed-453">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="da4ed-454">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="da4ed-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="da4ed-455">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="da4ed-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="da4ed-456">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="da4ed-457">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="da4ed-457">Dynamically select policies</span></span>

<span data-ttu-id="da4ed-458">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="da4ed-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="da4ed-459">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="da4ed-460">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="da4ed-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="da4ed-461">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="da4ed-462">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="da4ed-463">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="da4ed-464">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="da4ed-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="da4ed-465">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="da4ed-466">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="da4ed-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="da4ed-467">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="da4ed-468">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="da4ed-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="da4ed-469">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="da4ed-470">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="da4ed-471">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="da4ed-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="da4ed-472">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="da4ed-473">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="da4ed-474">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="da4ed-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="da4ed-475">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="da4ed-476">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="da4ed-477">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="da4ed-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="da4ed-478">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="da4ed-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="da4ed-479">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="da4ed-480">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="da4ed-481">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="da4ed-482">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="da4ed-483">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="da4ed-484">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="da4ed-485">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="da4ed-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="da4ed-486">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="da4ed-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="da4ed-487">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="da4ed-488">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="da4ed-489">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="da4ed-490">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="da4ed-491">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="da4ed-492">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-493">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="da4ed-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="da4ed-494">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="da4ed-495">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="da4ed-496">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="da4ed-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="da4ed-497">`IHttpClientFactory`O uso do em um aplicativo habilitado para di evita:</span><span class="sxs-lookup"><span data-stu-id="da4ed-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="da4ed-498">Problemas de esgotamento de recursos por instâncias de Pooling `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="da4ed-499">Problemas de DNS obsoletos por `HttpMessageHandler` instâncias de ciclo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="da4ed-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="da4ed-500">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de vida longa <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="da4ed-501">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="da4ed-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="da4ed-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="da4ed-503">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="da4ed-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="da4ed-504">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que são `IHttpClientFactory` resolvidos de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="da4ed-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="da4ed-505">O `SocketsHttpHandler` compartilha conexões entre `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="da4ed-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-506">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="da4ed-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="da4ed-507">O `SocketsHttpHandler` ciclos se conexões de acordo com o `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="da4ed-508">Cookies</span><span class="sxs-lookup"><span data-stu-id="da4ed-508">Cookies</span></span>

<span data-ttu-id="da4ed-509">As `HttpMessageHandler` instâncias em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="da4ed-510">O `CookieContainer` compartilhamento de objeto inesperado geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="da4ed-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="da4ed-511">Para aplicativos que exigem cookie s, considere o:</span><span class="sxs-lookup"><span data-stu-id="da4ed-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="da4ed-512">Desabilitando a cookie manipulação automática</span><span class="sxs-lookup"><span data-stu-id="da4ed-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="da4ed-513">Evitá`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="da4ed-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="da4ed-514">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar a cookie manipulação automática:</span><span class="sxs-lookup"><span data-stu-id="da4ed-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="da4ed-515">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="da4ed-515">Logging</span></span>

<span data-ttu-id="da4ed-516">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="da4ed-517">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="da4ed-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="da4ed-518">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="da4ed-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="da4ed-519">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="da4ed-520">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="da4ed-521">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="da4ed-522">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="da4ed-523">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="da4ed-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="da4ed-524">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="da4ed-525">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="da4ed-526">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="da4ed-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="da4ed-527">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="da4ed-528">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="da4ed-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="da4ed-529">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="da4ed-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="da4ed-530">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="da4ed-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="da4ed-531">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="da4ed-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="da4ed-532">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="da4ed-533">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="da4ed-534">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="da4ed-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="da4ed-535">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="da4ed-536">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="da4ed-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="da4ed-537">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="da4ed-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="da4ed-538">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="da4ed-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="da4ed-539">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="da4ed-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="da4ed-540">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="da4ed-540">In the following example:</span></span>

* <span data-ttu-id="da4ed-541"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="da4ed-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="da4ed-542">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="da4ed-543">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="da4ed-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="da4ed-544">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="da4ed-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="da4ed-545">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="da4ed-545">Additional resources</span></span>

* [<span data-ttu-id="da4ed-546">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="da4ed-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="da4ed-547">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="da4ed-548">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="da4ed-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="da4ed-549">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="da4ed-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="da4ed-550">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="da4ed-551">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="da4ed-551">It offers the following benefits:</span></span>

* <span data-ttu-id="da4ed-552">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-553">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="da4ed-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="da4ed-554">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="da4ed-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="da4ed-555">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="da4ed-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="da4ed-556">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="da4ed-557">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="da4ed-558">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da4ed-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="da4ed-559">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="da4ed-559">Prerequisites</span></span>

<span data-ttu-id="da4ed-560">Os projetos direcionados ao .NET Framework exigem a instalação do pacote do NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="da4ed-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="da4ed-561">Os projetos destinados ao .Net Core e a referência ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) já incluem o pacote `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="da4ed-562">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="da4ed-562">Consumption patterns</span></span>

<span data-ttu-id="da4ed-563">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="da4ed-564">Uso básico</span><span class="sxs-lookup"><span data-stu-id="da4ed-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="da4ed-565">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="da4ed-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="da4ed-566">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="da4ed-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="da4ed-567">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="da4ed-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="da4ed-568">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="da4ed-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="da4ed-569">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="da4ed-570">Uso básico</span><span class="sxs-lookup"><span data-stu-id="da4ed-570">Basic usage</span></span>

<span data-ttu-id="da4ed-571">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="da4ed-572">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da4ed-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="da4ed-573">O `IHttpClientFactory` pode ser usado para criar uma `HttpClient` instância do:</span><span class="sxs-lookup"><span data-stu-id="da4ed-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="da4ed-574">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="da4ed-575">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="da4ed-576">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="da4ed-577">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="da4ed-577">Named clients</span></span>

<span data-ttu-id="da4ed-578">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="da4ed-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="da4ed-579">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="da4ed-580">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="da4ed-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="da4ed-581">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="da4ed-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="da4ed-582">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="da4ed-583">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="da4ed-584">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="da4ed-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="da4ed-585">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="da4ed-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="da4ed-586">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="da4ed-587">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="da4ed-587">Typed clients</span></span>

<span data-ttu-id="da4ed-588">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-588">Typed clients:</span></span>

* <span data-ttu-id="da4ed-589">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="da4ed-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="da4ed-590">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="da4ed-591">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="da4ed-592">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="da4ed-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="da4ed-593">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="da4ed-594">Um cliente tipado aceita um `HttpClient` parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="da4ed-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="da4ed-595">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="da4ed-596">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="da4ed-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="da4ed-597">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="da4ed-598">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="da4ed-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="da4ed-599">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="da4ed-600">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="da4ed-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="da4ed-601">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="da4ed-602">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="da4ed-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="da4ed-603">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="da4ed-604">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="da4ed-605">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="da4ed-606">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="da4ed-607">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="da4ed-607">Generated clients</span></span>

<span data-ttu-id="da4ed-608">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="da4ed-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="da4ed-609">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="da4ed-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="da4ed-610">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="da4ed-611">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="da4ed-612">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="da4ed-612">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="da4ed-613">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="da4ed-613">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="da4ed-614">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="da4ed-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="da4ed-615">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="da4ed-615">Outgoing request middleware</span></span>

<span data-ttu-id="da4ed-616">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="da4ed-617">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="da4ed-618">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="da4ed-619">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="da4ed-620">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="da4ed-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="da4ed-621">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="da4ed-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="da4ed-622">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="da4ed-623">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="da4ed-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="da4ed-624">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="da4ed-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="da4ed-625">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="da4ed-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="da4ed-626">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="da4ed-627">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração de um `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="da4ed-628">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="da4ed-629">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="da4ed-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="da4ed-630">O manipulador **deve** ser registrado na injeção de dependência como serviço temporário, mas nunca com escopo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="da4ed-631">Se o manipulador estiver registrado como um serviço com escopo e algum serviço do qual ele depende for descartável:</span><span class="sxs-lookup"><span data-stu-id="da4ed-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="da4ed-632">Os serviços do manipulador poderão ser descartados antes que ele saia do escopo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="da4ed-633">Os serviços de manipulador descartados farão com que o manipulador falhe.</span><span class="sxs-lookup"><span data-stu-id="da4ed-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="da4ed-634">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando no tipo do manipulador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="da4ed-635">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="da4ed-636">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="da4ed-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="da4ed-637">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="da4ed-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="da4ed-638">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="da4ed-639">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="da4ed-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="da4ed-640">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="da4ed-641">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-641">Use Polly-based handlers</span></span>

<span data-ttu-id="da4ed-642">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="da4ed-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="da4ed-643">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="da4ed-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="da4ed-644">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="da4ed-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="da4ed-645">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-646">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="da4ed-646">The Polly extensions:</span></span>

* <span data-ttu-id="da4ed-647">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="da4ed-648">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="da4ed-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="da4ed-649">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="da4ed-650">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="da4ed-650">Handle transient faults</span></span>

<span data-ttu-id="da4ed-651">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="da4ed-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="da4ed-652">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="da4ed-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="da4ed-653">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="da4ed-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="da4ed-654">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="da4ed-655">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="da4ed-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="da4ed-656">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="da4ed-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="da4ed-657">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="da4ed-658">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="da4ed-658">Dynamically select policies</span></span>

<span data-ttu-id="da4ed-659">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="da4ed-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="da4ed-660">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="da4ed-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="da4ed-661">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="da4ed-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="da4ed-662">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="da4ed-663">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="da4ed-664">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="da4ed-665">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="da4ed-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="da4ed-666">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="da4ed-667">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="da4ed-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="da4ed-668">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="da4ed-669">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="da4ed-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="da4ed-670">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="da4ed-671">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="da4ed-672">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="da4ed-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="da4ed-673">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="da4ed-674">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="da4ed-675">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="da4ed-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="da4ed-676">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="da4ed-677">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="da4ed-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="da4ed-678">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="da4ed-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="da4ed-679">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="da4ed-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="da4ed-680">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="da4ed-681">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="da4ed-682">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="da4ed-683">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="da4ed-684">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="da4ed-685">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="da4ed-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="da4ed-686">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="da4ed-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="da4ed-687">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="da4ed-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="da4ed-688">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="da4ed-689">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="da4ed-690">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="da4ed-691">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="da4ed-692">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="da4ed-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="da4ed-693">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-694">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="da4ed-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="da4ed-695">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="da4ed-696">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="da4ed-697">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="da4ed-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="da4ed-698">`IHttpClientFactory`O uso do em um aplicativo habilitado para di evita:</span><span class="sxs-lookup"><span data-stu-id="da4ed-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="da4ed-699">Problemas de esgotamento de recursos por instâncias de Pooling `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="da4ed-700">Problemas de DNS obsoletos por `HttpMessageHandler` instâncias de ciclo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="da4ed-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="da4ed-701">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de vida longa <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="da4ed-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="da4ed-702">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="da4ed-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="da4ed-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="da4ed-704">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="da4ed-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="da4ed-705">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que são `IHttpClientFactory` resolvidos de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="da4ed-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="da4ed-706">O `SocketsHttpHandler` compartilha conexões entre `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="da4ed-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="da4ed-707">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="da4ed-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="da4ed-708">O `SocketsHttpHandler` ciclos se conexões de acordo com o `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="da4ed-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="da4ed-709">Cookies</span><span class="sxs-lookup"><span data-stu-id="da4ed-709">Cookies</span></span>

<span data-ttu-id="da4ed-710">As `HttpMessageHandler` instâncias em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="da4ed-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="da4ed-711">O `CookieContainer` compartilhamento de objeto inesperado geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="da4ed-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="da4ed-712">Para aplicativos que exigem cookie s, considere o:</span><span class="sxs-lookup"><span data-stu-id="da4ed-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="da4ed-713">Desabilitando a cookie manipulação automática</span><span class="sxs-lookup"><span data-stu-id="da4ed-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="da4ed-714">Evitá`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="da4ed-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="da4ed-715">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar a cookie manipulação automática:</span><span class="sxs-lookup"><span data-stu-id="da4ed-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="da4ed-716">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="da4ed-716">Logging</span></span>

<span data-ttu-id="da4ed-717">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="da4ed-718">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="da4ed-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="da4ed-719">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="da4ed-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="da4ed-720">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="da4ed-721">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="da4ed-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="da4ed-722">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="da4ed-723">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="da4ed-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="da4ed-724">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="da4ed-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="da4ed-725">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="da4ed-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="da4ed-726">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="da4ed-727">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="da4ed-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="da4ed-728">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="da4ed-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="da4ed-729">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="da4ed-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="da4ed-730">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="da4ed-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="da4ed-731">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="da4ed-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="da4ed-732">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="da4ed-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="da4ed-733">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="da4ed-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="da4ed-734">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="da4ed-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="da4ed-735">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="da4ed-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="da4ed-736">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="da4ed-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="da4ed-737">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="da4ed-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="da4ed-738">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="da4ed-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="da4ed-739">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="da4ed-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="da4ed-740">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="da4ed-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="da4ed-741">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="da4ed-741">In the following example:</span></span>

* <span data-ttu-id="da4ed-742"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="da4ed-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="da4ed-743">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="da4ed-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="da4ed-744">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="da4ed-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="da4ed-745">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="da4ed-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="da4ed-746">Middleware de propagação de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="da4ed-746">Header propagation middleware</span></span>

<span data-ttu-id="da4ed-747">A propagação de cabeçalho é um middleware com suporte da Comunidade para propagar cabeçalhos HTTP da solicitação de entrada para as solicitações de cliente HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="da4ed-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="da4ed-748">Para usar a propagação de cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="da4ed-748">To use header propagation:</span></span>

* <span data-ttu-id="da4ed-749">Referencie a porta com suporte da Comunidade do pacote [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="da4ed-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="da4ed-750">ASP.NET Core 3,1 e posterior dá suporte a [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="da4ed-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="da4ed-751">Configure o middleware e `HttpClient` em `Startup` :</span><span class="sxs-lookup"><span data-stu-id="da4ed-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="da4ed-752">O cliente inclui os cabeçalhos configurados nas solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="da4ed-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="da4ed-753">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="da4ed-753">Additional resources</span></span>

* [<span data-ttu-id="da4ed-754">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="da4ed-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="da4ed-755">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="da4ed-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="da4ed-756">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="da4ed-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
