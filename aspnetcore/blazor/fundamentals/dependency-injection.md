---
title: Injeção de dependência de ASP.NET Core Blazor
author: guardrex
description: Saiba como os Blazor aplicativos podem injetar serviços em componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 6f41cc102411377f144dd8e12f7942031c59619a
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975203"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="09b43-103">Injeção de dependência de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="09b43-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="09b43-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="09b43-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="09b43-105">[Injeção de dependência (di)](xref:fundamentals/dependency-injection) é uma técnica para acessar os serviços configurados em um local central:</span><span class="sxs-lookup"><span data-stu-id="09b43-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="09b43-106">Os serviços registrados na estrutura podem ser injetados diretamente em componentes de Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="09b43-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="09b43-107">Blazor os aplicativos definem e registram serviços personalizados e os tornam disponíveis em todo o aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="09b43-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="09b43-108">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="09b43-108">Default services</span></span>

<span data-ttu-id="09b43-109">Os serviços mostrados na tabela a seguir são comumente usados em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="09b43-109">The services shown in the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="09b43-110">Serviço</span><span class="sxs-lookup"><span data-stu-id="09b43-110">Service</span></span> | <span data-ttu-id="09b43-111">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="09b43-111">Lifetime</span></span> | <span data-ttu-id="09b43-112">Descrição</span><span class="sxs-lookup"><span data-stu-id="09b43-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="09b43-113">Com escopo</span><span class="sxs-lookup"><span data-stu-id="09b43-113">Scoped</span></span> | <p><span data-ttu-id="09b43-114">Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</span><span class="sxs-lookup"><span data-stu-id="09b43-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="09b43-115">A instância do <xref:System.Net.Http.HttpClient> em um Blazor WebAssembly aplicativo usa o navegador para manipular o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="09b43-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="09b43-116">Blazor Server os aplicativos não incluem um <xref:System.Net.Http.HttpClient> configurado como um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="09b43-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="09b43-117">Forneça um <xref:System.Net.Http.HttpClient> para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="09b43-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="09b43-118">Para obter mais informações, consulte <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="09b43-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="09b43-119">Um <xref:System.Net.Http.HttpClient> é registrado como um serviço com escopo, não singleton.</span><span class="sxs-lookup"><span data-stu-id="09b43-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="09b43-120">Para obter mais informações, consulte a seção [tempo de vida do serviço](#service-lifetime) .</span><span class="sxs-lookup"><span data-stu-id="09b43-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="09b43-121">**Blazor WebAssembly**: Singleton</span><span class="sxs-lookup"><span data-stu-id="09b43-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="09b43-122">**Blazor Server**: Com escopo</span><span class="sxs-lookup"><span data-stu-id="09b43-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="09b43-123">Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas.</span><span class="sxs-lookup"><span data-stu-id="09b43-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="09b43-124">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="09b43-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="09b43-125">**Blazor WebAssembly**: Singleton</span><span class="sxs-lookup"><span data-stu-id="09b43-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="09b43-126">**Blazor Server**: Com escopo</span><span class="sxs-lookup"><span data-stu-id="09b43-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="09b43-127">Contém auxiliares para trabalhar com URIs e estado de navegação.</span><span class="sxs-lookup"><span data-stu-id="09b43-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="09b43-128">Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="09b43-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="09b43-129">Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="09b43-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="09b43-130">Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="09b43-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="09b43-131">Adicionar serviços a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="09b43-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="09b43-132">Configure serviços para a coleção de serviços do aplicativo no `Program.Main` método de `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="09b43-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="09b43-133">No exemplo a seguir, a `MyDependency` implementação está registrada para `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="09b43-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

<span data-ttu-id="09b43-134">Depois que o host é criado, os serviços estão disponíveis no escopo de DI raiz antes de qualquer componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="09b43-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="09b43-135">Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:</span><span class="sxs-lookup"><span data-stu-id="09b43-135">This can be useful for running initialization logic before rendering content:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

<span data-ttu-id="09b43-136">O host fornece uma instância de configuração central para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="09b43-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="09b43-137">Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de configuração padrão (por exemplo, `appsettings.json` ) para `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="09b43-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

<span data-ttu-id="09b43-138">Depois de criar um novo aplicativo, examine o `Startup.ConfigureServices` método em `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="09b43-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="09b43-139">O <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> método é passado um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , que é uma lista de objetos do [descritor de serviço](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) .</span><span class="sxs-lookup"><span data-stu-id="09b43-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="09b43-140">Os serviços são adicionados ao `ConfigureServices` método fornecendo descritores de serviço à coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="09b43-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="09b43-141">O exemplo a seguir demonstra o conceito com a `IDataAccess` interface e sua implementação concreta `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="09b43-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="09b43-142">Tempo de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="09b43-142">Service lifetime</span></span>

<span data-ttu-id="09b43-143">Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="09b43-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="09b43-144">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="09b43-144">Lifetime</span></span> | <span data-ttu-id="09b43-145">Descrição</span><span class="sxs-lookup"><span data-stu-id="09b43-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="09b43-146">Blazor WebAssembly Atualmente, os aplicativos não têm um conceito de escopos de DI.</span><span class="sxs-lookup"><span data-stu-id="09b43-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="09b43-147">`Scoped`-serviços registrados se comportam como `Singleton` serviços.</span><span class="sxs-lookup"><span data-stu-id="09b43-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="09b43-148">O Blazor Server modelo de hospedagem dá suporte ao `Scoped` tempo de vida entre solicitações HTTP, mas não entre SignalR mensagens de conexão/circuito entre componentes que são carregados no cliente.</span><span class="sxs-lookup"><span data-stu-id="09b43-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SignalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="09b43-149">A Razor parte páginas ou MVC do aplicativo trata os serviços com escopo normalmente e recria os serviços em *cada solicitação HTTP* ao navegar entre páginas ou exibições, ou de uma página ou exibição para um componente.</span><span class="sxs-lookup"><span data-stu-id="09b43-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="09b43-150">Os serviços com escopo não são reconstruídos ao navegar entre componentes no cliente, em que a comunicação com o servidor ocorre pela SignalR conexão do circuito do usuário, não por meio de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="09b43-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="09b43-151">Nos cenários de componente a seguir no cliente, os serviços com escopo são reconstruídos porque um novo circuito é criado para o usuário:</span><span class="sxs-lookup"><span data-stu-id="09b43-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="09b43-152">O usuário fecha a janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="09b43-152">The user closes the browser's window.</span></span> <span data-ttu-id="09b43-153">O usuário abre uma nova janela e navega de volta para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="09b43-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="09b43-154">O usuário fecha a última guia do aplicativo em uma janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="09b43-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="09b43-155">O usuário abre uma nova guia e navega de volta para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="09b43-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="09b43-156">O usuário seleciona o botão recarregar/atualizar do navegador.</span><span class="sxs-lookup"><span data-stu-id="09b43-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="09b43-157">Para obter mais informações sobre como preservar o estado do usuário entre serviços com escopo em Blazor Server aplicativos, consulte <xref:blazor/hosting-models?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="09b43-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="09b43-158">DI cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="09b43-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="09b43-159">Todos os componentes que exigem um `Singleton` serviço recebem uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="09b43-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="09b43-160">Sempre que um componente Obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma *nova instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="09b43-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="09b43-161">O sistema de DI é baseado no sistema de injeção de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="09b43-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="09b43-162">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="09b43-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="09b43-163">Solicitar um serviço em um componente</span><span class="sxs-lookup"><span data-stu-id="09b43-163">Request a service in a component</span></span>

<span data-ttu-id="09b43-164">Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando a [`@inject`](xref:mvc/views/razor#inject) Razor diretiva, que tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="09b43-164">After services are added to the service collection, inject the services into the components using the [`@inject`](xref:mvc/views/razor#inject) Razor directive, which has two parameters:</span></span>

* <span data-ttu-id="09b43-165">Tipo: o tipo do serviço a injetar.</span><span class="sxs-lookup"><span data-stu-id="09b43-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="09b43-166">Propriedade: o nome da propriedade que recebe o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="09b43-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="09b43-167">A propriedade não requer criação manual.</span><span class="sxs-lookup"><span data-stu-id="09b43-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="09b43-168">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="09b43-168">The compiler creates the property.</span></span>

<span data-ttu-id="09b43-169">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="09b43-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="09b43-170">Use várias [`@inject`](xref:mvc/views/razor#inject) instruções para injetar serviços diferentes.</span><span class="sxs-lookup"><span data-stu-id="09b43-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="09b43-171">O exemplo a seguir mostra como usar [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="09b43-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="09b43-172">A implementação do serviço `Services.IDataAccess` é injetada na Propriedade do componente `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="09b43-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="09b43-173">Observe como o código está usando apenas a `IDataAccess` abstração:</span><span class="sxs-lookup"><span data-stu-id="09b43-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="09b43-174">Internamente, a propriedade gerada ( `DataRepository` ) usa o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="09b43-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="09b43-175">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="09b43-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="09b43-176">Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo:</span><span class="sxs-lookup"><span data-stu-id="09b43-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="09b43-177">Em componentes derivados da classe base, a [`@inject`](xref:mvc/views/razor#inject) diretiva não é necessária.</span><span class="sxs-lookup"><span data-stu-id="09b43-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="09b43-178">O <xref:Microsoft.AspNetCore.Components.InjectAttribute> da classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="09b43-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="09b43-179">Usar DI em serviços</span><span class="sxs-lookup"><span data-stu-id="09b43-179">Use DI in services</span></span>

<span data-ttu-id="09b43-180">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="09b43-180">Complex services might require additional services.</span></span> <span data-ttu-id="09b43-181">No exemplo a seguir, `DataAccess` requer o <xref:System.Net.Http.HttpClient> serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="09b43-181">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="09b43-182">[`@inject`](xref:mvc/views/razor#inject) (ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo) não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="09b43-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="09b43-183">A *injeção de Construtor* deve ser usada em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="09b43-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="09b43-184">Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="09b43-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="09b43-185">Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo.</span><span class="sxs-lookup"><span data-stu-id="09b43-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="09b43-186">No exemplo a seguir, o Construtor recebe um <xref:System.Net.Http.HttpClient> via di.</span><span class="sxs-lookup"><span data-stu-id="09b43-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="09b43-187"><xref:System.Net.Http.HttpClient> é um serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="09b43-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="09b43-188">Pré-requisitos para injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="09b43-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="09b43-189">Um construtor deve existir cujos argumentos podem ser todos atendidos por DI.</span><span class="sxs-lookup"><span data-stu-id="09b43-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="09b43-190">Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.</span><span class="sxs-lookup"><span data-stu-id="09b43-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="09b43-191">O Construtor aplicável deve ser `public` .</span><span class="sxs-lookup"><span data-stu-id="09b43-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="09b43-192">Um Construtor aplicável deve existir.</span><span class="sxs-lookup"><span data-stu-id="09b43-192">One applicable constructor must exist.</span></span> <span data-ttu-id="09b43-193">No caso de uma ambiguidade, DI gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="09b43-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="09b43-194">Classes de componente base do utilitário para gerenciar um escopo de DI</span><span class="sxs-lookup"><span data-stu-id="09b43-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="09b43-195">Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="09b43-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="09b43-196">Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI.</span><span class="sxs-lookup"><span data-stu-id="09b43-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="09b43-197">Em Blazor Server aplicativos, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="09b43-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="09b43-198">Em Blazor WebAssembly aplicativos, os serviços registrados com um tempo de vida no escopo são tratados como singletons, portanto, eles vivem mais do que os serviços com escopo em aplicativos ASP.NET Core típicos.</span><span class="sxs-lookup"><span data-stu-id="09b43-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="09b43-199">Para detectar serviços descartáveis em um aplicativo, consulte a seção [detectar descartáveis transitórios](#detect-transient-disposables) .</span><span class="sxs-lookup"><span data-stu-id="09b43-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="09b43-200">Uma abordagem que limita um tempo de vida do serviço em Blazor aplicativos é o uso do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo.</span><span class="sxs-lookup"><span data-stu-id="09b43-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="09b43-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> é um tipo abstrato derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que cria um escopo de di correspondente ao tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="09b43-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="09b43-202">Usando esse escopo, é possível usar os serviços de DI com um tempo de vida de escopo e tê-los ativos, desde que o componente.</span><span class="sxs-lookup"><span data-stu-id="09b43-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="09b43-203">Quando o componente é destruído, os serviços do provedor de serviço no escopo do componente também são descartados.</span><span class="sxs-lookup"><span data-stu-id="09b43-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="09b43-204">Isso pode ser útil para serviços que:</span><span class="sxs-lookup"><span data-stu-id="09b43-204">This can be useful for services that:</span></span>

* <span data-ttu-id="09b43-205">Deve ser reutilizado dentro de um componente, pois o tempo de vida transitório é inadequado.</span><span class="sxs-lookup"><span data-stu-id="09b43-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="09b43-206">Não devem ser compartilhados entre componentes, pois o tempo de vida singleton é inadequado.</span><span class="sxs-lookup"><span data-stu-id="09b43-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="09b43-207">Duas versões do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="09b43-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="09b43-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> é um filho abstrato e descartável do <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo com uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade protegida do tipo <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="09b43-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="09b43-209">Esse provedor pode ser usado para resolver serviços que têm o escopo definido para o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="09b43-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="09b43-210">Os serviços de DI injetados no componente usando [`@inject`](xref:mvc/views/razor#inject) ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo não são criados no escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="09b43-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="09b43-211">Para usar o escopo do componente, os serviços devem ser resolvidos usando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> ou o <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="09b43-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="09b43-212">Todos os serviços resolvidos usando o <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provedor têm suas dependências fornecidas do mesmo escopo.</span><span class="sxs-lookup"><span data-stu-id="09b43-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <span data-ttu-id="09b43-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e adiciona uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriedade que retorna uma instância do do `T` provedor de injeção de escopo.</span><span class="sxs-lookup"><span data-stu-id="09b43-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="09b43-214">Esse tipo é uma maneira conveniente de acessar serviços com escopo sem usar uma instância do <xref:System.IServiceProvider> quando há um serviço primário que o aplicativo requer do contêiner di usando o escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="09b43-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="09b43-215">A <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade está disponível, portanto, o aplicativo pode obter serviços de outros tipos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="09b43-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="09b43-216">Uso de um Entity Framework Core (EF Core) DbContext de DI</span><span class="sxs-lookup"><span data-stu-id="09b43-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="09b43-217">Para obter mais informações, consulte <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="09b43-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="09b43-218">Detectar descartáveis transitórios</span><span class="sxs-lookup"><span data-stu-id="09b43-218">Detect transient disposables</span></span>

<span data-ttu-id="09b43-219">Os exemplos a seguir mostram como detectar serviços descartáveis indetectáveis em um aplicativo que deve usar o <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="09b43-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="09b43-220">Para obter mais informações, consulte a seção [classes de componente base do utilitário para gerenciar um escopo de di](#utility-base-component-classes-to-manage-a-di-scope) .</span><span class="sxs-lookup"><span data-stu-id="09b43-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="09b43-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="09b43-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="09b43-222">O `TransientDisposable` no exemplo a seguir é detectado ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="09b43-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="09b43-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="09b43-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="09b43-224">Adicione o namespace para <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="09b43-224">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="09b43-225">Em `Program.CreateHostBuilder` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="09b43-225">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

<span data-ttu-id="09b43-226">O `TransientDependency` no exemplo a seguir é detectado ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="09b43-226">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

<span data-ttu-id="09b43-227">O aplicativo pode registrar descartáveis transitórias sem lançar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="09b43-227">The app can register transient disposables without throwing an exception.</span></span> <span data-ttu-id="09b43-228">No entanto, tentar resolver os resultados descartáveis transitórios em um <xref:System.InvalidOperationException> , como mostra o exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="09b43-228">However, attempting to resolve a transient disposable results in an <xref:System.InvalidOperationException>, as the following example shows.</span></span>

<span data-ttu-id="09b43-229">`Pages/TransientDisposable.razor`:</span><span class="sxs-lookup"><span data-stu-id="09b43-229">`Pages/TransientDisposable.razor`:</span></span>

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

<span data-ttu-id="09b43-230">Navegue até o `TransientDisposable` componente em `/transient-disposable` e um <xref:System.InvalidOperationException> é gerado quando a estrutura tenta construir uma instância do `TransientDisposable` :</span><span class="sxs-lookup"><span data-stu-id="09b43-230">Navigate to the `TransientDisposable` component at `/transient-disposable` and an <xref:System.InvalidOperationException> is thrown when the framework attempts to construct an instance of `TransientDisposable`:</span></span>

> <span data-ttu-id="09b43-231">System. InvalidOperationException: tentando resolver o serviço descartável transitório TransientDisposable no escopo errado.</span><span class="sxs-lookup"><span data-stu-id="09b43-231">System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope.</span></span> <span data-ttu-id="09b43-232">Use uma \<T> classe base de componente ' OwningComponentBase ' para o serviço ' T' que você está tentando resolver.</span><span class="sxs-lookup"><span data-stu-id="09b43-232">Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09b43-233">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="09b43-233">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="09b43-234">`IDisposable` Diretrizes para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="09b43-234">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
