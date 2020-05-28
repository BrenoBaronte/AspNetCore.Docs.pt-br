---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="dependency-injection-in-aspnet-core"></a>Injeção de dependência no ASP.NET Core

Por [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)

::: moniker range=">= aspnetcore-3.0"

O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Visão geral da injeção de dependência

Uma *dependência* é qualquer objeto exigido por outro objeto. Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe. A classe  é uma dependência da classe:

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

A classe cria e depende diretamente da instância `MyDependency`. As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:

* Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.
* Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe. Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.
* É difícil testar a unidade dessa implementação. O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.

Injeção de dependência trata desses problemas da seguinte maneira:

* O uso de uma interface ou classe base para abstrair a implementação da dependência.
* Registrando a dependência em um contêiner de serviço. O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>. Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.
* *Injeção* do serviço no construtor da classe na qual ele é usado. A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.

No [exemplo de aplicativo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Essa interface é implementada por um tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor. Não é incomum usar a injeção de dependência de uma maneira encadeada. Por sua vez, cada dependência solicitada solicita suas próprias dependências. O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido. O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.

`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço. `IMyDependency` está registrado em `Startup.ConfigureServices`. `ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.

O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`. O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação. Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços. Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária. Recomendamos que os aplicativos sigam essa convenção. Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.

Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular. O campo é usado para acessar o serviço conforme o necessário na classe.

No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Serviços injetados na inicialização

Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Os serviços podem ser injetados em `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Serviços fornecidos pela estrutura

O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC. Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host). Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura. Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.

| Tipo de serviço | Tempo de vida |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitório | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitório | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitório | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitório | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrar serviços adicionais com métodos de extensão

Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço. O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Escolha um tempo de vida apropriado para cada serviço registrado. Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:

### <a name="transient"></a>Transitório

Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço. Esse tempo de vida funciona melhor para serviços leves e sem estado.

### <a name="scoped"></a>Com escopo

Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).

> [!WARNING]
> Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`. Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton. Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço). Cada solicitação subsequente usa a mesma instância. Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço. Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.

> [!WARNING]
> É perigoso resolver um serviço com escopo de um singleton. Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.

## <a name="service-registration-methods"></a>Métodos de registro do serviço

Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.

| Método | Automática<br>objeto<br>descarte | Vários<br>implementações | Passar argumentos |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

--- | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Exemplo:<br>`services.AddSingleton<IMyDep, MyDep>();`| Sim | Sim | Não | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Sim | Sim | Sim | |`Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Exemplo:<br>`services.AddSingleton<MyDep>();`| Sim | Não | Não | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Não | Sim | Sim | |`AddSingleton(new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));`| Não | Não | Sim |

Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services). Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).

Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.

No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`. A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Para obter mais informações, consulte:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*. Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`. Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada. Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.

No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`. A segunda linha registra `MyDep` para `IMyDep2`. A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Comportamento da injeção de construtor

Os serviços podem ser resolvidos por dois mecanismos:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência. `ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.

Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.

Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .

Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista. Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.

## <a name="entity-framework-contexts"></a>Contextos de Entity Framework

Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente. O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados. Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.

## <a name="lifetime-and-registration-options"></a>Opções de tempo de vida e de registro

Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`. Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

As interfaces são implementadas na classe `Operation`. O construtor `Operation` gera um GUID, caso um não seja fornecido:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos. Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.

* Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`. `OperationService` recebe uma nova instância da classe `IOperationTransient`. A nova instância produz outro `OperationId`.
* Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente. Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.
* Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`. Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).

O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais. O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`. Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

As duas saídas a seguir mostram os resultados das duas solicitações:

**Primeira solicitação:**

Operações do controlador:

Transitória: d233e165-f417-469b-a866-1cf1935d2518  
Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

`OperationService` operações:

Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

**Segunda solicitação:**

Operações do controlador:

Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

`OperationService` operações:

Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:

* Os objetos *transitórios* sempre são diferentes. O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente. Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.
* Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.
* Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.

## <a name="call-services-from-main"></a>Chamar os serviços desde o principal

Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo. Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização. O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a>Validação de escopo

Quando o aplicativo está em execução no ambiente de desenvolvimento e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviço padrão executa verificações para verificar se:

* Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.
* Os serviços com escopo não são injetados direta ou indiretamente em singletons.

O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado. O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.

Os serviços com escopo são descartados pelo contêiner que os criou. Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado. A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.

Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Serviços de solicitação

Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo. Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.

Em geral, o aplicativo não deve usar essas propriedades diretamente. Em vez disso, solicite os tipos que as classes exigem por meio de construtores de classe e permita que a estrutura Insira as dependências. Isso resulta em classes que são mais fáceis de testar.

> [!NOTE]
> Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Projetar serviços para injeção de dependência

As melhores práticas são:

* Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.
* Evite classes e membros com estado e estáticos. Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.
* Evite a instanciação direta das classes dependentes em serviços. A instanciação direta acopla o código a uma implementação específica.
* Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.

Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe. Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário. Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Descarte de serviços

O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele. Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.

No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

No exemplo a seguir:

* As instâncias de serviço não são criadas pelo contêiner de serviço.
* Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.
* A estrutura não descarta os serviços automaticamente.
* Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

Para obter uma discussão sobre as opções de descarte de serviço, consulte [quatro maneiras de descartar idisposables em ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).

## <a name="default-service-container-replacement"></a>Substituição do contêiner de serviço padrão

O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor. É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:

* Injeção de propriedade
* Injeção com base no nome
* Contêineres filho
* Gerenciamento de tempo de vida personalizado
* Suporte de `Func<T>` para inicialização lenta
* Registro baseado em Convenção

Os contêineres de terceiros a seguir podem ser usados com aplicativos ASP.NET Core:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Acesso thread-safe

Crie serviços singleton thread-safe. Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.

O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe. Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.

## <a name="recommendations"></a>Recomendações

* A resolução de serviço baseada em `async/await` e `Task` não é compatível. O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.

* Evite armazenar dados e a configuração diretamente no contêiner do serviço. Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço. A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options). Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto. É melhor solicitar o item real por meio da DI.

* Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).

* Evite usar o *padrão do localizador de serviço*. Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:

  **Incorreto**

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  **Correto**:

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime. Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).

* Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação. As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.

A DI é uma *alternativa* aos padrões de acesso a objeto estático/global. Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Padrões recomendados para multilocação em DI

O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornece multilocação. Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).

Consulte os aplicativos de exemplos em https://github.com/OrchardCMS/OrchardCore.Samples para obter exemplos de como criar aplicativos modulares e multilocatários usando o Orchard Core Framework sem nenhum dos recursos específicos do CMS.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Princípio de Dependências Explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Como registrar um serviço com várias interfaces na DI do ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Visão geral da injeção de dependência

Uma *dependência* é qualquer objeto exigido por outro objeto. Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe. A classe  é uma dependência da classe:

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

A classe cria e depende diretamente da instância `MyDependency`. As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:

* Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.
* Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe. Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.
* É difícil testar a unidade dessa implementação. O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.

Injeção de dependência trata desses problemas da seguinte maneira:

* O uso de uma interface ou classe base para abstrair a implementação da dependência.
* Registrando a dependência em um contêiner de serviço. O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>. Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.
* *Injeção* do serviço no construtor da classe na qual ele é usado. A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.

No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Essa interface é implementada por um tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor. Não é incomum usar a injeção de dependência de uma maneira encadeada. Por sua vez, cada dependência solicitada solicita suas próprias dependências. O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido. O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.

`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço. `IMyDependency` está registrado em `Startup.ConfigureServices`. `ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.

O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`. O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação. Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços. Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária. Recomendamos que os aplicativos sigam essa convenção. Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.

Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular. O campo é usado para acessar o serviço conforme o necessário na classe.

No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Serviços injetados na inicialização

Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Os serviços podem ser injetados em `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Serviços fornecidos pela estrutura

O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC. Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host). Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura. Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.

| Tipo de serviço | Tempo de vida |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitório | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitório | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitório | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitório | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrar serviços adicionais com métodos de extensão

Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço. O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Escolha um tempo de vida apropriado para cada serviço registrado. Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:

### <a name="transient"></a>Transitório

Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço. Esse tempo de vida funciona melhor para serviços leves e sem estado.

### <a name="scoped"></a>Com escopo

Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).

> [!WARNING]
> Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`. Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton. Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço). Cada solicitação subsequente usa a mesma instância. Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço. Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.

> [!WARNING]
> É perigoso resolver um serviço com escopo de um singleton. Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.

## <a name="service-registration-methods"></a>Métodos de registro do serviço

Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.

| Método | Automática<br>objeto<br>descarte | Vários<br>implementações | Passar argumentos |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

--- | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Exemplo:<br>`services.AddSingleton<IMyDep, MyDep>();`| Sim | Sim | Não | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Sim | Sim | Sim | |`Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Exemplo:<br>`services.AddSingleton<MyDep>();`| Sim | Não | Não | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Não | Sim | Sim | |`AddSingleton(new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));`| Não | Não | Sim |

Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services). Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).

Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.

No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`. A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Para obter mais informações, consulte:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*. Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`. Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada. Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.

No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`. A segunda linha registra `MyDep` para `IMyDep2`. A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Comportamento da injeção de construtor

Os serviços podem ser resolvidos por dois mecanismos:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência. `ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.

Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.

Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .

Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista. Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.

## <a name="entity-framework-contexts"></a>Contextos de Entity Framework

Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente. O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados. Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.

## <a name="lifetime-and-registration-options"></a>Opções de tempo de vida e de registro

Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`. Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

As interfaces são implementadas na classe `Operation`. O construtor `Operation` gera um GUID, caso um não seja fornecido:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos. Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.

* Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`. `OperationService` recebe uma nova instância da classe `IOperationTransient`. A nova instância produz outro `OperationId`.
* Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente. Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.
* Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`. Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).

O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais. O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`. Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

As duas saídas a seguir mostram os resultados das duas solicitações:

**Primeira solicitação:**

Operações do controlador:

Transitória: d233e165-f417-469b-a866-1cf1935d2518  
Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

`OperationService` operações:

Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

**Segunda solicitação:**

Operações do controlador:

Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

`OperationService` operações:

Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:

* Os objetos *transitórios* sempre são diferentes. O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente. Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.
* Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.
* Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.

## <a name="call-services-from-main"></a>Chamar os serviços desde o principal

Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo. Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização. O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a>Validação de escopo

Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:

* Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.
* Os serviços com escopo não são injetados direta ou indiretamente em singletons.

O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado. O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.

Os serviços com escopo são descartados pelo contêiner que os criou. Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado. A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.

Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Serviços de solicitação

Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo. Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.

Em geral, o aplicativo não deve usar essas propriedades diretamente. Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências. Isso resulta em classes que são mais fáceis de testar.

> [!NOTE]
> Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Projetar serviços para injeção de dependência

As melhores práticas são:

* Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.
* Evite classes e membros com estado e estáticos. Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.
* Evite a instanciação direta das classes dependentes em serviços. A instanciação direta acopla o código a uma implementação específica.
* Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.

Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe. Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário. Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Descarte de serviços

O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele. Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.

No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

No exemplo a seguir:

* As instâncias de serviço não são criadas pelo contêiner de serviço.
* Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.
* A estrutura não descarta os serviços automaticamente.
* Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a>Substituição do contêiner de serviço padrão

O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor. É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:

* Injeção de propriedade
* Injeção com base no nome
* Contêineres filho
* Gerenciamento de tempo de vida personalizado
* Suporte de `Func<T>` para inicialização lenta
* Registro baseado em Convenção

Os contêineres de terceiros a seguir podem ser usados com aplicativos ASP.NET Core:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Acesso thread-safe

Crie serviços singleton thread-safe. Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.

O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe. Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.

## <a name="recommendations"></a>Recomendações

* A resolução de serviço baseada em `async/await` e `Task` não é compatível. O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.

* Evite armazenar dados e a configuração diretamente no contêiner do serviço. Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço. A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options). Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto. É melhor solicitar o item real por meio da DI.

* Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).

* Evite usar o *padrão de localizador de serviço*, que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .

  * Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:

    **Incorreto**

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    **Correto**:

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .

* Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação. As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.

A DI é uma *alternativa* aos padrões de acesso a objeto estático/global. Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Princípio de Dependências Explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Como registrar um serviço com várias interfaces na DI do ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
