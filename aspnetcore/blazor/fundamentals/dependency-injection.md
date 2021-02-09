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
# <a name="aspnet-core-blazor-dependency-injection"></a>Injeção de dependência de ASP.NET Core Blazor

Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)

[Injeção de dependência (di)](xref:fundamentals/dependency-injection) é uma técnica para acessar os serviços configurados em um local central:

* Os serviços registrados na estrutura podem ser injetados diretamente em componentes de Blazor aplicativos.
* Blazor os aplicativos definem e registram serviços personalizados e os tornam disponíveis em todo o aplicativo por meio de DI.

## <a name="default-services"></a>Serviços padrão

Os serviços mostrados na tabela a seguir são comumente usados em Blazor aplicativos.

| Serviço | Tempo de vida | Descrição |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Com escopo | <p>Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</p><p>A instância do <xref:System.Net.Http.HttpClient> em um Blazor WebAssembly aplicativo usa o navegador para manipular o tráfego HTTP em segundo plano.</p><p>Blazor Server os aplicativos não incluem um <xref:System.Net.Http.HttpClient> configurado como um serviço por padrão. Forneça um <xref:System.Net.Http.HttpClient> para um Blazor Server aplicativo.</p><p>Para obter mais informações, consulte <xref:blazor/call-web-api>.</p><p>Um <xref:System.Net.Http.HttpClient> é registrado como um serviço com escopo, não singleton. Para obter mais informações, consulte a seção [tempo de vida do serviço](#service-lifetime) .</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly**: Singleton</p><p>**Blazor Server**: Com escopo</p> | Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas. Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly**: Singleton</p><p>**Blazor Server**: Com escopo</p> | Contém auxiliares para trabalhar com URIs e estado de navegação. Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela. Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.

## <a name="add-services-to-an-app"></a>Adicionar serviços a um aplicativo

::: zone pivot="webassembly"

Configure serviços para a coleção de serviços do aplicativo no `Program.Main` método de `Program.cs` . No exemplo a seguir, a `MyDependency` implementação está registrada para `IMyDependency` :

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

Depois que o host é criado, os serviços estão disponíveis no escopo de DI raiz antes de qualquer componente ser renderizado. Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

O host fornece uma instância de configuração central para o aplicativo. Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de configuração padrão (por exemplo, `appsettings.json` ) para `InitializeWeatherAsync` :

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

Depois de criar um novo aplicativo, examine o `Startup.ConfigureServices` método em `Startup.cs` :

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

O <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> método é passado um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , que é uma lista de objetos do [descritor de serviço](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) . Os serviços são adicionados ao `ConfigureServices` método fornecendo descritores de serviço à coleção de serviços. O exemplo a seguir demonstra o conceito com a `IDataAccess` interface e sua implementação concreta `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>Tempo de vida do serviço

Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.

| Tempo de vida | Descrição |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Blazor WebAssembly Atualmente, os aplicativos não têm um conceito de escopos de DI. `Scoped`-serviços registrados se comportam como `Singleton` serviços.</p><p>O Blazor Server modelo de hospedagem dá suporte ao `Scoped` tempo de vida entre solicitações HTTP, mas não entre SignalR mensagens de conexão/circuito entre componentes que são carregados no cliente. A Razor parte páginas ou MVC do aplicativo trata os serviços com escopo normalmente e recria os serviços em *cada solicitação HTTP* ao navegar entre páginas ou exibições, ou de uma página ou exibição para um componente. Os serviços com escopo não são reconstruídos ao navegar entre componentes no cliente, em que a comunicação com o servidor ocorre pela SignalR conexão do circuito do usuário, não por meio de solicitações HTTP. Nos cenários de componente a seguir no cliente, os serviços com escopo são reconstruídos porque um novo circuito é criado para o usuário:</p><ul><li>O usuário fecha a janela do navegador. O usuário abre uma nova janela e navega de volta para o aplicativo.</li><li>O usuário fecha a última guia do aplicativo em uma janela do navegador. O usuário abre uma nova guia e navega de volta para o aplicativo.</li><li>O usuário seleciona o botão recarregar/atualizar do navegador.</li></ul><p>Para obter mais informações sobre como preservar o estado do usuário entre serviços com escopo em Blazor Server aplicativos, consulte <xref:blazor/hosting-models?pivots=server> .</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI cria uma *única instância* do serviço. Todos os componentes que exigem um `Singleton` serviço recebem uma instância do mesmo serviço. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Sempre que um componente Obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma *nova instância* do serviço. |

O sistema de DI é baseado no sistema de injeção de ASP.NET Core. Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Solicitar um serviço em um componente

Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando a [`@inject`](xref:mvc/views/razor#inject) Razor diretiva, que tem dois parâmetros:

* Tipo: o tipo do serviço a injetar.
* Propriedade: o nome da propriedade que recebe o serviço de aplicativo injetado. A propriedade não requer criação manual. O compilador cria a propriedade.

Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.

Use várias [`@inject`](xref:mvc/views/razor#inject) instruções para injetar serviços diferentes.

O exemplo a seguir mostra como usar [`@inject`](xref:mvc/views/razor#inject) . A implementação do serviço `Services.IDataAccess` é injetada na Propriedade do componente `DataRepository` . Observe como o código está usando apenas a `IDataAccess` abstração:

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

Internamente, a propriedade gerada ( `DataRepository` ) usa o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo. Normalmente, esse atributo não é usado diretamente. Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo:

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

Em componentes derivados da classe base, a [`@inject`](xref:mvc/views/razor#inject) diretiva não é necessária. O <xref:Microsoft.AspNetCore.Components.InjectAttribute> da classe base é suficiente:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Usar DI em serviços

Serviços complexos podem exigir serviços adicionais. No exemplo a seguir, `DataAccess` requer o <xref:System.Net.Http.HttpClient> serviço padrão. [`@inject`](xref:mvc/views/razor#inject) (ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo) não está disponível para uso em serviços. A *injeção de Construtor* deve ser usada em seu lugar. Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço. Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo. No exemplo a seguir, o Construtor recebe um <xref:System.Net.Http.HttpClient> via di. <xref:System.Net.Http.HttpClient> é um serviço padrão.

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

Pré-requisitos para injeção de construtor:

* Um construtor deve existir cujos argumentos podem ser todos atendidos por DI. Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.
* O Construtor aplicável deve ser `public` .
* Um Construtor aplicável deve existir. No caso de uma ambiguidade, DI gera uma exceção.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classes de componente base do utilitário para gerenciar um escopo de DI

Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual. Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI. Em Blazor Server aplicativos, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado. Em Blazor WebAssembly aplicativos, os serviços registrados com um tempo de vida no escopo são tratados como singletons, portanto, eles vivem mais do que os serviços com escopo em aplicativos ASP.NET Core típicos.

> [!NOTE]
> Para detectar serviços descartáveis em um aplicativo, consulte a seção [detectar descartáveis transitórios](#detect-transient-disposables) .

Uma abordagem que limita um tempo de vida do serviço em Blazor aplicativos é o uso do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo. <xref:Microsoft.AspNetCore.Components.OwningComponentBase> é um tipo abstrato derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que cria um escopo de di correspondente ao tempo de vida do componente. Usando esse escopo, é possível usar os serviços de DI com um tempo de vida de escopo e tê-los ativos, desde que o componente. Quando o componente é destruído, os serviços do provedor de serviço no escopo do componente também são descartados. Isso pode ser útil para serviços que:

* Deve ser reutilizado dentro de um componente, pois o tempo de vida transitório é inadequado.
* Não devem ser compartilhados entre componentes, pois o tempo de vida singleton é inadequado.

Duas versões do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo estão disponíveis:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> é um filho abstrato e descartável do <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo com uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade protegida do tipo <xref:System.IServiceProvider> . Esse provedor pode ser usado para resolver serviços que têm o escopo definido para o tempo de vida do componente.

  Os serviços de DI injetados no componente usando [`@inject`](xref:mvc/views/razor#inject) ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo não são criados no escopo do componente. Para usar o escopo do componente, os serviços devem ser resolvidos usando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> ou o <xref:System.IServiceProvider.GetService%2A> . Todos os serviços resolvidos usando o <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provedor têm suas dependências fornecidas do mesmo escopo.

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e adiciona uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriedade que retorna uma instância do do `T` provedor de injeção de escopo. Esse tipo é uma maneira conveniente de acessar serviços com escopo sem usar uma instância do <xref:System.IServiceProvider> quando há um serviço primário que o aplicativo requer do contêiner di usando o escopo do componente. A <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade está disponível, portanto, o aplicativo pode obter serviços de outros tipos, se necessário.

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>Uso de um Entity Framework Core (EF Core) DbContext de DI

Para obter mais informações, consulte <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Detectar descartáveis transitórios

Os exemplos a seguir mostram como detectar serviços descartáveis indetectáveis em um aplicativo que deve usar o <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Para obter mais informações, consulte a seção [classes de componente base do utilitário para gerenciar um escopo de di](#utility-base-component-classes-to-manage-a-di-scope) .

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

O `TransientDisposable` no exemplo a seguir é detectado ( `Program.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

Adicione o namespace para <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> para `Program.cs` :

```csharp
using Microsoft.Extensions.DependencyInjection;
```

Em `Program.CreateHostBuilder` `Program.cs` :

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

O `TransientDependency` no exemplo a seguir é detectado ( `Startup.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

O aplicativo pode registrar descartáveis transitórias sem lançar uma exceção. No entanto, tentar resolver os resultados descartáveis transitórios em um <xref:System.InvalidOperationException> , como mostra o exemplo a seguir.

`Pages/TransientDisposable.razor`:

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

Navegue até o `TransientDisposable` componente em `/transient-disposable` e um <xref:System.InvalidOperationException> é gerado quando a estrutura tenta construir uma instância do `TransientDisposable` :

> System. InvalidOperationException: tentando resolver o serviço descartável transitório TransientDisposable no escopo errado. Use uma \<T> classe base de componente ' OwningComponentBase ' para o serviço ' T' que você está tentando resolver.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/dependency-injection>
* [`IDisposable` Diretrizes para instâncias transitórias e compartilhadas](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
