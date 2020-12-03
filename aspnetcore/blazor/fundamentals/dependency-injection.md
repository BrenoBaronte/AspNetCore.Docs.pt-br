---
title: Injeção de dependência de ASP.NET Core Blazor
author: guardrex
description: Saiba como os Blazor aplicativos podem injetar serviços em componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: c68deb5237754872e11bfd9c83275b9a3b147319
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556509"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="fc723-103">Injeção de dependência de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="fc723-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="fc723-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="fc723-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="fc723-105">Blazor dá suporte à [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc723-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fc723-106">Os aplicativos podem usar serviços internos injetando-os em componentes.</span><span class="sxs-lookup"><span data-stu-id="fc723-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="fc723-107">Os aplicativos também podem definir e registrar serviços personalizados e torná-los disponíveis em todo o aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="fc723-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="fc723-108">DI é uma técnica para acessar os serviços configurados em um local central.</span><span class="sxs-lookup"><span data-stu-id="fc723-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="fc723-109">Isso pode ser útil em Blazor aplicativos para:</span><span class="sxs-lookup"><span data-stu-id="fc723-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="fc723-110">Compartilhe uma única instância de uma classe de serviço em vários componentes, conhecido como um serviço *singleton* .</span><span class="sxs-lookup"><span data-stu-id="fc723-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="fc723-111">Dissociar componentes de classes de serviço concretas usando abstrações de referência.</span><span class="sxs-lookup"><span data-stu-id="fc723-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="fc723-112">Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc723-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="fc723-113">A interface é implementada por uma `DataAccess` classe concreta e registrada como um serviço no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc723-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="fc723-114">Quando um componente usa DI para receber uma `IDataAccess` implementação, o componente não é acoplado ao tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="fc723-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="fc723-115">A implementação pode ser trocada, talvez para uma implementação fictícia em testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="fc723-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="fc723-116">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="fc723-116">Default services</span></span>

<span data-ttu-id="fc723-117">Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc723-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="fc723-118">Serviço</span><span class="sxs-lookup"><span data-stu-id="fc723-118">Service</span></span> | <span data-ttu-id="fc723-119">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="fc723-119">Lifetime</span></span> | <span data-ttu-id="fc723-120">Descrição</span><span class="sxs-lookup"><span data-stu-id="fc723-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="fc723-121">Com escopo</span><span class="sxs-lookup"><span data-stu-id="fc723-121">Scoped</span></span> | <span data-ttu-id="fc723-122">Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</span><span class="sxs-lookup"><span data-stu-id="fc723-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="fc723-123">A instância do <xref:System.Net.Http.HttpClient> em um Blazor WebAssembly aplicativo usa o navegador para manipular o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="fc723-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="fc723-124">Blazor Server os aplicativos não incluem um <xref:System.Net.Http.HttpClient> configurado como um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="fc723-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="fc723-125">Forneça um <xref:System.Net.Http.HttpClient> para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc723-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="fc723-126">Para obter mais informações, consulte <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="fc723-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="fc723-127">Um <xref:System.Net.Http.HttpClient> é registrado como um serviço com escopo, não singleton.</span><span class="sxs-lookup"><span data-stu-id="fc723-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="fc723-128">Para obter mais informações, consulte a seção [tempo de vida do serviço](#service-lifetime) .</span><span class="sxs-lookup"><span data-stu-id="fc723-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="fc723-129">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="fc723-129">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="fc723-130">Com escopo ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="fc723-130">Scoped (Blazor Server)</span></span> | <span data-ttu-id="fc723-131">Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas.</span><span class="sxs-lookup"><span data-stu-id="fc723-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="fc723-132">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="fc723-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="fc723-133">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="fc723-133">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="fc723-134">Com escopo ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="fc723-134">Scoped (Blazor Server)</span></span> | <span data-ttu-id="fc723-135">Contém auxiliares para trabalhar com URIs e estado de navegação.</span><span class="sxs-lookup"><span data-stu-id="fc723-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="fc723-136">Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="fc723-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="fc723-137">Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="fc723-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="fc723-138">Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="fc723-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="fc723-139">Adicionar serviços a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="fc723-139">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="fc723-140">Configure serviços para a coleção de serviços do aplicativo no `Main` método de `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="fc723-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="fc723-141">No exemplo a seguir, a `MyDependency` implementação está registrada para `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="fc723-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<IMyDependency, MyDependency>();

        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="fc723-142">Depois que o host é criado, os serviços podem ser acessados do escopo de DI raiz antes de qualquer componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="fc723-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="fc723-143">Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:</span><span class="sxs-lookup"><span data-stu-id="fc723-143">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="fc723-144">O host também fornece uma instância de configuração central para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc723-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="fc723-145">Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de configuração padrão (por exemplo, `appsettings.json` ) para `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="fc723-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="fc723-146">Depois de criar um novo aplicativo, examine o `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="fc723-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="fc723-147">O <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> método é passado um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , que é uma lista de objetos do descritor de serviço ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="fc723-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="fc723-148">Os serviços são adicionados ao `ConfigureServices` método fornecendo descritores de serviço à coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="fc723-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="fc723-149">O exemplo a seguir demonstra o conceito com a `IDataAccess` interface e sua implementação concreta `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="fc723-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="fc723-150">Tempo de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="fc723-150">Service lifetime</span></span>

<span data-ttu-id="fc723-151">Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="fc723-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="fc723-152">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="fc723-152">Lifetime</span></span> | <span data-ttu-id="fc723-153">Descrição</span><span class="sxs-lookup"><span data-stu-id="fc723-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="fc723-154">Blazor WebAssembly Atualmente, os aplicativos não têm um conceito de escopos de DI.</span><span class="sxs-lookup"><span data-stu-id="fc723-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="fc723-155">`Scoped`-serviços registrados se comportam como `Singleton` serviços.</span><span class="sxs-lookup"><span data-stu-id="fc723-155">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="fc723-156">O Blazor Server modelo de hospedagem dá suporte ao `Scoped` tempo de vida entre solicitações HTTP, mas não em mensagens de conexão/circuito SingalR entre componentes que são carregados no cliente.</span><span class="sxs-lookup"><span data-stu-id="fc723-156">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="fc723-157">A Razor parte páginas ou MVC do aplicativo trata os serviços com escopo normalmente e recria os serviços em *cada solicitação HTTP* ao navegar entre páginas ou exibições, ou de uma página ou exibição para um componente.</span><span class="sxs-lookup"><span data-stu-id="fc723-157">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="fc723-158">Os serviços com escopo não são reconstruídos ao navegar entre componentes no cliente, em que a comunicação com o servidor ocorre pela SignalR conexão do circuito do usuário, não por meio de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="fc723-158">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="fc723-159">Nos cenários de componente a seguir no cliente, os serviços com escopo são reconstruídos porque um novo circuito é criado para o usuário:</span><span class="sxs-lookup"><span data-stu-id="fc723-159">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="fc723-160">O usuário fecha a janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="fc723-160">The user closes the browser's window.</span></span> <span data-ttu-id="fc723-161">O usuário abre uma nova janela e navega de volta para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc723-161">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="fc723-162">O usuário fecha a última guia do aplicativo em uma janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="fc723-162">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="fc723-163">O usuário abre uma nova guia e navega de volta para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc723-163">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="fc723-164">O usuário seleciona o botão recarregar/atualizar do navegador.</span><span class="sxs-lookup"><span data-stu-id="fc723-164">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="fc723-165">Para obter mais informações sobre como preservar o estado do usuário entre serviços com escopo em Blazor Server aplicativos, consulte <xref:blazor/hosting-models?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="fc723-165">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="fc723-166">DI cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="fc723-166">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="fc723-167">Todos os componentes que exigem um `Singleton` serviço recebem uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="fc723-167">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="fc723-168">Sempre que um componente Obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma *nova instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="fc723-168">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="fc723-169">O sistema de DI é baseado no sistema de injeção de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc723-169">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="fc723-170">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="fc723-170">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="fc723-171">Solicitar um serviço em um componente</span><span class="sxs-lookup"><span data-stu-id="fc723-171">Request a service in a component</span></span>

<span data-ttu-id="fc723-172">Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando a diretiva de [ \@ inserção](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="fc723-172">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="fc723-173">[`@inject`](xref:mvc/views/razor#inject) tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="fc723-173">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="fc723-174">Tipo: o tipo do serviço a injetar.</span><span class="sxs-lookup"><span data-stu-id="fc723-174">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="fc723-175">Propriedade: o nome da propriedade que recebe o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="fc723-175">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="fc723-176">A propriedade não requer criação manual.</span><span class="sxs-lookup"><span data-stu-id="fc723-176">The property doesn't require manual creation.</span></span> <span data-ttu-id="fc723-177">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="fc723-177">The compiler creates the property.</span></span>

<span data-ttu-id="fc723-178">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="fc723-178">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="fc723-179">Use várias [`@inject`](xref:mvc/views/razor#inject) instruções para injetar serviços diferentes.</span><span class="sxs-lookup"><span data-stu-id="fc723-179">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="fc723-180">O exemplo a seguir mostra como usar [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="fc723-180">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="fc723-181">A implementação do serviço `Services.IDataAccess` é injetada na Propriedade do componente `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="fc723-181">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="fc723-182">Observe como o código está usando apenas a `IDataAccess` abstração:</span><span class="sxs-lookup"><span data-stu-id="fc723-182">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="fc723-183">Internamente, a propriedade gerada ( `DataRepository` ) usa o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="fc723-183">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="fc723-184">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="fc723-184">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="fc723-185">Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo:</span><span class="sxs-lookup"><span data-stu-id="fc723-185">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="fc723-186">Em componentes derivados da classe base, a [`@inject`](xref:mvc/views/razor#inject) diretiva não é necessária.</span><span class="sxs-lookup"><span data-stu-id="fc723-186">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="fc723-187">O <xref:Microsoft.AspNetCore.Components.InjectAttribute> da classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="fc723-187">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="fc723-188">Usar DI em serviços</span><span class="sxs-lookup"><span data-stu-id="fc723-188">Use DI in services</span></span>

<span data-ttu-id="fc723-189">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="fc723-189">Complex services might require additional services.</span></span> <span data-ttu-id="fc723-190">No exemplo anterior, `DataAccess` pode exigir o <xref:System.Net.Http.HttpClient> serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="fc723-190">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="fc723-191">[`@inject`](xref:mvc/views/razor#inject) (ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo) não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="fc723-191">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="fc723-192">A *injeção de Construtor* deve ser usada em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="fc723-192">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="fc723-193">Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="fc723-193">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="fc723-194">Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo.</span><span class="sxs-lookup"><span data-stu-id="fc723-194">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="fc723-195">No exemplo a seguir, o Construtor recebe um <xref:System.Net.Http.HttpClient> via di.</span><span class="sxs-lookup"><span data-stu-id="fc723-195">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="fc723-196"><xref:System.Net.Http.HttpClient> é um serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="fc723-196"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="fc723-197">Pré-requisitos para injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="fc723-197">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="fc723-198">Um construtor deve existir cujos argumentos podem ser todos atendidos por DI.</span><span class="sxs-lookup"><span data-stu-id="fc723-198">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="fc723-199">Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.</span><span class="sxs-lookup"><span data-stu-id="fc723-199">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="fc723-200">O Construtor aplicável deve ser `public` .</span><span class="sxs-lookup"><span data-stu-id="fc723-200">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="fc723-201">Um Construtor aplicável deve existir.</span><span class="sxs-lookup"><span data-stu-id="fc723-201">One applicable constructor must exist.</span></span> <span data-ttu-id="fc723-202">No caso de uma ambiguidade, DI gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="fc723-202">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="fc723-203">Classes de componente base do utilitário para gerenciar um escopo de DI</span><span class="sxs-lookup"><span data-stu-id="fc723-203">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="fc723-204">Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="fc723-204">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="fc723-205">Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI.</span><span class="sxs-lookup"><span data-stu-id="fc723-205">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="fc723-206">Em Blazor Server aplicativos, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="fc723-206">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="fc723-207">Em Blazor WebAssembly aplicativos, os serviços registrados com um tempo de vida no escopo são tratados como singletons, portanto, eles vivem mais do que os serviços com escopo em aplicativos ASP.NET Core típicos.</span><span class="sxs-lookup"><span data-stu-id="fc723-207">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="fc723-208">Para detectar serviços descartáveis em um aplicativo, consulte a seção [detectar descartáveis transitórios](#detect-transient-disposables) .</span><span class="sxs-lookup"><span data-stu-id="fc723-208">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="fc723-209">Uma abordagem que limita um tempo de vida do serviço em Blazor aplicativos é o uso do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo.</span><span class="sxs-lookup"><span data-stu-id="fc723-209">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="fc723-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> é um tipo abstrato derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que cria um escopo de di correspondente ao tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="fc723-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="fc723-211">Usando esse escopo, é possível usar os serviços de DI com um tempo de vida de escopo e tê-los ativos, desde que o componente.</span><span class="sxs-lookup"><span data-stu-id="fc723-211">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="fc723-212">Quando o componente é destruído, os serviços do provedor de serviço no escopo do componente também são descartados.</span><span class="sxs-lookup"><span data-stu-id="fc723-212">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="fc723-213">Isso pode ser útil para serviços que:</span><span class="sxs-lookup"><span data-stu-id="fc723-213">This can be useful for services that:</span></span>

* <span data-ttu-id="fc723-214">Deve ser reutilizado dentro de um componente, pois o tempo de vida transitório é inadequado.</span><span class="sxs-lookup"><span data-stu-id="fc723-214">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="fc723-215">Não devem ser compartilhados entre componentes, pois o tempo de vida singleton é inadequado.</span><span class="sxs-lookup"><span data-stu-id="fc723-215">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="fc723-216">Duas versões do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="fc723-216">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="fc723-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> é um filho abstrato e descartável do <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo com uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade protegida do tipo <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="fc723-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="fc723-218">Esse provedor pode ser usado para resolver serviços que têm o escopo definido para o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="fc723-218">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="fc723-219">Os serviços de DI injetados no componente usando [`@inject`](xref:mvc/views/razor#inject) ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo não são criados no escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="fc723-219">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="fc723-220">Para usar o escopo do componente, os serviços devem ser resolvidos usando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> ou o <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="fc723-220">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="fc723-221">Todos os serviços resolvidos usando o <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provedor têm suas dependências fornecidas do mesmo escopo.</span><span class="sxs-lookup"><span data-stu-id="fc723-221">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="fc723-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e adiciona uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriedade que retorna uma instância do do `T` provedor de injeção de escopo.</span><span class="sxs-lookup"><span data-stu-id="fc723-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="fc723-223">Esse tipo é uma maneira conveniente de acessar serviços com escopo sem usar uma instância do <xref:System.IServiceProvider> quando há um serviço primário que o aplicativo requer do contêiner di usando o escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="fc723-223">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="fc723-224">A <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade está disponível, portanto, o aplicativo pode obter serviços de outros tipos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="fc723-224">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="fc723-225">Uso de um Entity Framework Core (EF Core) DbContext de DI</span><span class="sxs-lookup"><span data-stu-id="fc723-225">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="fc723-226">Para obter mais informações, consulte <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="fc723-226">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="fc723-227">Detectar descartáveis transitórios</span><span class="sxs-lookup"><span data-stu-id="fc723-227">Detect transient disposables</span></span>

<span data-ttu-id="fc723-228">Os exemplos a seguir mostram como detectar serviços descartáveis indetectáveis em um aplicativo que deve usar o <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="fc723-228">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="fc723-229">Para obter mais informações, consulte a seção [classes de componente base do utilitário para gerenciar um escopo de di](#utility-base-component-classes-to-manage-a-di-scope) .</span><span class="sxs-lookup"><span data-stu-id="fc723-229">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="fc723-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="fc723-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="fc723-231">O `TransientDisposable` no exemplo a seguir é detectado ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="fc723-231">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

### Blazor Server

<span data-ttu-id="fc723-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="fc723-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="fc723-233">`Program`:</span><span class="sxs-lookup"><span data-stu-id="fc723-233">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="fc723-234">O `TransientDependency` no exemplo a seguir é detectado ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="fc723-234">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="fc723-235">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fc723-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="fc723-236">`IDisposable` Diretrizes para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="fc723-236">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
