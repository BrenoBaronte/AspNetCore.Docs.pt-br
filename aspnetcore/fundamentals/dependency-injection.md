---
title: Injeção de dependência no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 53ccb90e92b99385fcc1d9358686b505ac1a0dcc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060515"
---
# <a name="dependency-injection-in-aspnet-core"></a>Injeção de dependência no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)

O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.

Para obter informações sobre como usar a injeção de dependência em aplicativos diferentes de aplicativos Web, consulte [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection).

Para obter mais informações sobre injeção de dependência de opções, consulte <xref:fundamentals/configuration/options> .

Este tópico fornece informações sobre injeção de dependência no ASP.NET Core. A documentação principal sobre como usar a injeção de dependência está contida na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Visão geral da injeção de dependência

Uma *dependência* é um objeto do qual outro objeto depende. Examine a seguinte `MyDependency` classe com um `WriteMessage` método de que outras classes dependem:

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

Uma classe pode criar uma instância da `MyDependency` classe para fazer uso de seu `WriteMessage` método. No exemplo a seguir, a `MyDependency` classe é uma dependência da `IndexModel` classe:

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

A classe cria e depende diretamente da `MyDependency` classe. As dependências de código, como no exemplo anterior, são problemáticas e devem ser evitadas pelos seguintes motivos:

* Para substituir `MyDependency` por uma implementação diferente, a `IndexModel` classe deve ser modificada.
* Se `MyDependency` tiver dependências, elas também deverão ser configuradas pela `IndexModel` classe. Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.
* É difícil testar a unidade dessa implementação. O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.

Injeção de dependência trata desses problemas da seguinte maneira:

* O uso de uma interface ou classe base para abstrair a implementação da dependência.
* Registrando a dependência em um contêiner de serviço. O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>. Normalmente, os serviços são registrados no método do aplicativo `Startup.ConfigureServices` .
* *Injeção* do serviço no construtor da classe na qual ele é usado. A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.

No [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a `IMyDependency` interface define o `WriteMessage` método:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Essa interface é implementada por um tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

O aplicativo de exemplo registra o `IMyDependency` serviço com o tipo concreto `MyDependency` . O <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> método registra o serviço com um tempo de vida de escopo, o tempo de vida de uma única solicitação. Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

No aplicativo de exemplo, o `IMyDependency` serviço é solicitado e usado para chamar o `WriteMessage` método:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

Usando o padrão DI, o controlador:

* Não usa o tipo concreto `MyDependency` , apenas a `IMyDependency` interface que ele implementa. Isso facilita a alteração da implementação que o controlador usa sem modificar o controlador.
* Não cria uma instância do `MyDependency` , ela é criada pelo contêiner de di.

A implementação da `IMyDependency` interface pode ser aprimorada usando a API de registro em log interna:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

O `ConfigureServices` método atualizado registra a nova `IMyDependency` implementação:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2` depende de <xref:Microsoft.Extensions.Logging.ILogger%601> , que ele solicita no construtor. `ILogger<TCategoryName>` é um [serviço fornecido pelo Framework](#framework-provided-services).

Não é incomum usar a injeção de dependência de uma maneira encadeada. Por sua vez, cada dependência solicitada solicita suas próprias dependências. O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido. O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência* , *grafo de dependência* ou *grafo de objeto* .

O contêiner resolve aproveitando `ILogger<TCategoryName>` os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar cada [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).

Na terminologia de injeção de dependência, um serviço:

* Normalmente é um objeto que fornece um serviço para outros objetos, como o `IMyDependency` serviço.
* O não está relacionado a um serviço Web, embora o serviço possa usar um serviço Web.

A estrutura fornece um sistema de [registro em log](xref:fundamentals/logging/index) robusto. As `IMyDependency` implementações mostradas nos exemplos anteriores foram escritas para demonstrar a di básica, não para implementar o registro em log. A maioria dos aplicativos não deve precisar gravar agentes. O código a seguir demonstra como usar o log padrão, que não exige que nenhum serviço seja registrado no `ConfigureServices` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

Usando o código anterior, não há necessidade de atualizar `ConfigureServices` , porque o [registro em log](xref:fundamentals/logging/index) é fornecido pela estrutura.

## <a name="services-injected-into-startup"></a>Serviços injetados na inicialização

Os serviços podem ser injetados no `Startup` Construtor e no `Startup.Configure` método.

Somente os seguintes serviços podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Qualquer serviço registrado com o contêiner DI pode ser injetado no `Startup.Configure` método:

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

Para obter mais informações, consulte <xref:fundamentals/startup> e [acessar a configuração na inicialização](xref:fundamentals/configuration/index#access-configuration-in-startup).

## <a name="register-groups-of-services-with-extension-methods"></a>Registrar grupos de serviços com métodos de extensão

O ASP.NET Core Framework usa uma Convenção para registrar um grupo de serviços relacionados. A Convenção é usar um método de `Add{GROUP_NAME}` extensão único para registrar todos os serviços exigidos por um recurso de estrutura. Por exemplo, o <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. addcontrollers> método de extensão registra os serviços necessários para controladores MVC.

O código a seguir é gerado pelo Razor modelo de páginas usando contas de usuário individuais e mostra como adicionar serviços adicionais ao contêiner usando os métodos de extensão <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Confira [tempos de vida de serviço](/dotnet/core/extensions/dependency-injection#service-lifetimes) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)

Para usar serviços com escopo no middleware, use uma das seguintes abordagens:

* Injetar o serviço no `Invoke` método ou do middleware `InvokeAsync` . O uso de [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) gera uma exceção de tempo de execução porque força o serviço de escopo a se comportar como um singleton. O exemplo na seção de [Opções de tempo de vida e de registro](#lifetime-and-registration-options) demonstra a `InvokeAsync` abordagem.
* Use o [middleware baseado em fábrica](xref:fundamentals/middleware/extensibility). O middleware registrado usando essa abordagem é ativado por solicitação do cliente (conexão), que permite que os serviços com escopo sejam injetados no método do middleware `InvokeAsync` .

Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

## <a name="service-registration-methods"></a>Métodos de registro do serviço

Consulte [métodos de registro de serviço](/dotnet/core/extensions/dependency-injection#service-registration-methods) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)

 É comum usar várias implementações ao [simular tipos para teste](xref:test/integration-tests#inject-mock-services).

O registro de um serviço com apenas um tipo de implementação é equivalente ao registro desse serviço com a mesma implementação e tipo de serviço. É por isso que várias implementações de um serviço não podem ser registradas usando os métodos que não usam um tipo de serviço explícito. Esses métodos podem registrar várias *instâncias* de um serviço, mas todos terão o mesmo tipo de *implementação* .

Qualquer um dos métodos de registro de serviço acima pode ser usado para registrar várias instâncias de serviço do mesmo tipo de serviço. No exemplo a seguir, `AddSingleton` é chamado duas vezes com `IMyDependency` como o tipo de serviço. A segunda chamada para `AddSingleton` substitui a anterior quando resolvida como `IMyDependency` e a adiciona à anterior quando vários serviços são resolvidos por meio de `IEnumerable<IMyDependency>` . Os serviços aparecem na ordem em que foram registrados quando resolvidos por meio de `IEnumerable<{SERVICE}>` .

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a>Comportamento da injeção de construtor

Consulte [comportamento de injeção de Construtor](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)

## <a name="entity-framework-contexts"></a>Contextos de Entity Framework

Por padrão, os contextos de Entity Framework são adicionados ao contêiner de serviço usando o [tempo de vida do escopo](#service-lifetimes) porque as operações de banco de dados do aplicativo Web normalmente são delimitadas à solicitação do cliente. Para usar um tempo de vida diferente, especifique o tempo de vida usando uma <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> sobrecarga. Os serviços de um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida menor do que o tempo de vida do serviço.

## <a name="lifetime-and-registration-options"></a>Opções de tempo de vida e de registro

Para demonstrar a diferença entre tempos de vida de serviço e suas opções de registro, considere as seguintes interfaces que representam uma tarefa como uma operação com um identificador, `OperationId` . Dependendo de como o tempo de vida do serviço de uma operação está configurado para as seguintes interfaces, o contêiner fornecerá as mesmas instâncias do serviço ou as mesmas quando solicitado por uma classe:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

A classe a seguir `Operation` implementa todas as interfaces anteriores. O `Operation` Construtor gera um GUID e armazena os quatro últimos caracteres na `OperationId` Propriedade:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

O `Startup.ConfigureServices` método cria vários registros da classe de `Operation` acordo com os tempos de vida nomeados:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

O aplicativo de exemplo demonstra tempos de vida de objeto dentro e entre solicitações. O `IndexModel` e o middleware solicitam cada tipo de `IOperation` tipo e registram o `OperationId` para cada um:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

Semelhante ao `IndexModel` , o middleware resolve os mesmos serviços:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

Os serviços com escopo devem ser resolvidos no `InvokeAsync` método:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

A saída do agente mostra:

* Os objetos *transitórios* sempre são diferentes. O `OperationId` valor transitório é diferente no `IndexModel` e no middleware.
* Os objetos com *escopo* são os mesmos para cada solicitação, mas diferentes em cada solicitação.
* Os objetos *singleton* são os mesmos para cada solicitação.

Para reduzir a saída de log, defina "log: LogLevel: Microsoft: Error" na *appsettings.Development.jsno* arquivo:

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>Chamar os serviços desde o principal

Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) para resolver um serviço com escopo dentro do escopo do aplicativo. Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.

O exemplo a seguir mostra como acessar o serviço com escopo `IMyDependency` e chamar seu `WriteMessage` método em `Program.Main` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>Validação de escopo

Consulte [comportamento de injeção de Construtor](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)

Para obter mais informações, confira [Validação de escopo](xref:fundamentals/host/web-host#scope-validation).

## <a name="request-services"></a>Serviços de solicitação

Os serviços disponíveis em uma solicitação de ASP.NET Core são expostos por meio da coleção [HttpContext. Requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) . Quando os serviços são solicitados de dentro de uma solicitação, os serviços e suas dependências são resolvidos na `RequestServices` coleção.

A estrutura cria um escopo por solicitação e `RequestServices` expõe o provedor de serviço com escopo. Todos os serviços com escopo são válidos enquanto a solicitação está ativa.

> [!NOTE]
> Prefira solicitar dependências como parâmetros de construtor para resolver serviços da `RequestServices` coleção. Isso resulta em classes que são mais fáceis de testar.

## <a name="design-services-for-dependency-injection"></a>Projetar serviços para injeção de dependência

Ao projetar serviços para injeção de dependência:

* Evite classes e membros com estado e estáticos. Evite criar o estado global criando aplicativos para usar os serviços singleton.
* Evite a instanciação direta das classes dependentes em serviços. A instanciação direta acopla o código a uma implementação específica.
* Torne os serviços pequenos, bem fatorados e facilmente testados.

Se uma classe tiver muitas dependências injetadas, pode ser um sinal de que a classe tem muitas responsabilidades e viola o [princípio de responsabilidade única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Tente refatorar a classe movendo algumas de suas responsabilidades para novas classes. Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.

### <a name="disposal-of-services"></a>Descarte de serviços

O contêiner chama <xref:System.IDisposable.Dispose%2A> para os tipos <xref:System.IDisposable> criados por ele. Os serviços resolvidos do contêiner nunca devem ser descartados pelo desenvolvedor. Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton automaticamente.

No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

O console de depuração mostra a saída a seguir após cada atualização da página de índice:

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>Serviços não criados pelo contêiner de serviço

Considere o seguinte código:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

No código anterior:

* As instâncias de serviço não são criadas pelo contêiner de serviço.
* A estrutura não descarta os serviços automaticamente.
* O desenvolvedor é responsável por descartar os serviços.

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Diretrizes de IDisposable para instâncias transitórias e compartilhadas

Consulte as [diretrizes de IDisposable para a instância transitória e compartilhada](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)

## <a name="default-service-container-replacement"></a>Substituição do contêiner de serviço padrão

Consulte [substituição do contêiner de serviço padrão](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) na [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)

## <a name="recommendations"></a>Recomendações

Consulte as [recomendações](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) em [injeção de dependência no .net](/dotnet/core/extensions/dependency-injection)

* Evite usar o *padrão do localizador de serviço* . Por exemplo, não invoque <xref:System.IServiceProvider.GetService%2A> para obter uma instância de serviço quando for possível usar a DI:

  **Incorreto:**

    ![Código incorreto](dependency-injection/_static/bad.png)

  **Correto** :

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

<a name="ASP0000"></a>
* Evite chamadas para <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> no `ConfigureServices` . Chamar `BuildServiceProvider` normalmente acontece quando o desenvolvedor deseja resolver um serviço no `ConfigureServices` . Por exemplo, considere o caso em que o `LoginPath` é carregado a partir da configuração. Evite a seguinte abordagem:

  ![código inadequado chamando BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  Na imagem anterior, selecionar a linha ondulada verde em `services.BuildServiceProvider` mostra o seguinte aviso de ASP0000:

  > ASP0000 chamar ' BuildServiceProvider ' do código do aplicativo resulta em uma cópia adicional dos serviços singleton que estão sendo criados. Considere alternativas como a injeção de dependência de serviços como parâmetros para ' Configurar '.

  `BuildServiceProvider`A chamada cria um segundo contêiner, que pode criar singletons rasgados e causar referências a grafos de objeto em vários contêineres.

  Uma maneira correta de `LoginPath` se obter é usar o suporte interno do padrão de opções para di:

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* Serviços inposados transitórios são capturados pelo contêiner para descarte. Isso pode transformar em um vazamento de memória se resolvido no contêiner de nível superior.
* Habilite a validação de escopo para certificar-se de que o aplicativo não tem singletons que capturam serviços com escopo. Para obter mais informações, confira [Validação de escopo](#scope-validation).

Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação. As exceções são raras, principalmente os casos especiais dentro da própria estrutura.

A DI é uma *alternativa* aos padrões de acesso a objeto estático/global. Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Padrões recomendados para multilocação em DI

O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) é uma estrutura de aplicativo para a criação de aplicativos modulares multilocatários em ASP.NET Core. Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).

Consulte os [exemplos do Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) para obter exemplos de como criar aplicativos modulares e multilocatários usando apenas a estrutura do Orchard Core sem qualquer um de seus recursos específicos do CMS.

## <a name="framework-provided-services"></a>Serviços fornecidos pela estrutura

O `Startup.ConfigureServices` método registra os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC. Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host). Para aplicativos baseados nos modelos de ASP.NET Core, a estrutura registra mais de 250 serviços. 

A tabela a seguir lista uma pequena amostra desses serviços registrados no Framework:

| Tipo de Serviço                                                                                    | Tempo de vida  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitório |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | Transitório |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | Transitório |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | Transitório |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | Singleton |

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [Padrões de conferência NDC para desenvolvimento de aplicativo de DI](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Quatro maneiras de descartar IDisposables em ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Princípio de Dependências Explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Como registrar um serviço com várias interfaces na DI do ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)

O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

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

`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor. Não é incomum usar a injeção de dependência de uma maneira encadeada. Por sua vez, cada dependência solicitada solicita suas próprias dependências. O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido. O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência* , *grafo de dependência* ou *grafo de objeto* .

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

| Tipo de Serviço | Tempo de vida |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitório |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitório |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitório |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitório |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

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

Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.

### <a name="scoped"></a>Com escopo

Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).

Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.

> [!WARNING]
> Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`. Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton. Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço). Cada solicitação subsequente usa a mesma instância. Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço. Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.

Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.

> [!WARNING]
> É perigoso resolver um serviço com escopo de um singleton. Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.

## <a name="service-registration-methods"></a>Métodos de registro do serviço

Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.

| Método | Automática<br>objeto<br>descarte | Vários<br>implementações | Passar argumentos |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Exemplo:<br>`services.AddSingleton<IMyDep, MyDep>();` | Sim | Sim | Não |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Sim | Sim | Sim |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Exemplo:<br>`services.AddSingleton<MyDep>();` | Sim | Não | Não |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Não | Sim | Sim |
| `AddSingleton(new {IMPLEMENTATION})`<br>Exemplos:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Não | Não | Sim |

Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services). Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).

O registro de um serviço com apenas um tipo de implementação é equivalente ao registro desse serviço com a mesma implementação e tipo de serviço. É por isso que várias implementações de um serviço não podem ser registradas usando os métodos que não usam um tipo de serviço explícito. Esses métodos podem registrar várias *instâncias* de um serviço, mas todos terão o mesmo tipo de *implementação* .

Qualquer um dos métodos de registro de serviço acima pode ser usado para registrar várias instâncias de serviço do mesmo tipo de serviço. No exemplo a seguir, `AddSingleton` é chamado duas vezes com `IMyDependency` como o tipo de serviço. A segunda chamada para `AddSingleton` substitui a anterior quando resolvida como `IMyDependency` e a adiciona à anterior quando vários serviços são resolvidos por meio de `IEnumerable<IMyDependency>` . Os serviços aparecem na ordem em que foram registrados quando resolvidos por meio de `IEnumerable<{SERVICE}>` .

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

A estrutura também fornece `TryAdd{LIFETIME}` métodos de extensão, que registram o serviço somente se ainda não houver uma implementação registrada.

No exemplo a seguir, a chamada para `AddSingleton` registra `MyDependency` como uma implementação para `IMyDependency` . A chamada para `TryAddSingleton` não tem efeito porque `IMyDependency` já tem uma implementação registrada.

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

Para obter mais informações, consulte:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo* . Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`. Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada. Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.

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

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Diretrizes de IDisposable para instâncias transitórias e compartilhadas

#### <a name="transient-limited-lifetime"></a>Tempo de vida transitório, limitado

**Cenário**

O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:

* A instância é resolvida no escopo raiz.
* A instância deve ser descartada antes do término do escopo.

**Solução**

Use o padrão de fábrica para criar uma instância fora do escopo pai. Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente. Se o tipo final tiver outras dependências, a fábrica poderá:

* Receber um <xref:System.IServiceProvider> em seu construtor.
* Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.

#### <a name="shared-instance-limited-lifetime"></a>Instância compartilhada, tempo de vida limitado

**Cenário**

O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.

**Solução**

Registre a instância com um tempo de vida de escopo. Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários. Descarte o escopo quando o tempo de vida deve terminar.

#### <a name="general-guidelines"></a>Diretrizes gerais

* Não registre <xref:System.IDisposable> instâncias com um escopo transitório. Em vez disso, use o padrão de fábrica.
* Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz. A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.
* O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> . O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.
* Os escopos devem ser usados para controlar o tempo de vida dos serviços. Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.

## <a name="default-service-container-replacement"></a>Substituição do contêiner de serviço padrão

O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor. É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:

* Injeção de propriedade
* Injeção com base no nome
* Contêineres filho
* Gerenciamento de tempo de vida personalizado
* Suporte de `Func<T>` para inicialização lenta
* Registro baseado em Convenção

Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:

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
* Evite o acesso estático aos serviços. Por exemplo, Evite digitar estaticamente [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para uso em outro lugar.

* Evite usar o *padrão de localizador de serviço* , que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .
  * Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:

    **Incorreto:**

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    **Correto** :

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

Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação. As exceções são raras, principalmente os casos especiais dentro da própria estrutura.

A DI é uma *alternativa* aos padrões de acesso a objeto estático/global. Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Quatro maneiras de descartar IDisposables em ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Princípio de Dependências Explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Como registrar um serviço com várias interfaces na DI do ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
