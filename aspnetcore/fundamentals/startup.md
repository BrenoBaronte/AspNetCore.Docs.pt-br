---
title: Inicialização de aplicativo no ASP.NET Core
author: rick-anderson
description: Saiba como a classe Startup no ASP.NET Core configura serviços e o pipeline de solicitação do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/startup
ms.openlocfilehash: e3249df4b7388beeff13fe4b4e0ff481c35725c5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667653"
---
# <a name="app-startup-in-aspnet-core"></a>Inicialização de aplicativo no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com)

A classe `Startup` configura serviços e o pipeline de solicitação do aplicativo.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>A classe Startup

Os aplicativos do ASP.NET Core usam uma classe `Startup`, que é chamada de `Startup` por convenção. A classe `Startup`:

* Opcionalmente, inclua um método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> para configurar os *serviços* do aplicativo. Um serviço é um componente reutilizável que fornece a funcionalidade do aplicativo. Os serviços `ConfigureServices` são registrados e *consumidos* em todo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>o aplicativo via injeção de [dependência (DI)](xref:fundamentals/dependency-injection) ou .
* Inclui um método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> para criar o pipeline de processamento de solicitações do aplicativo.

`ConfigureServices` e `Configure` são chamados pelo runtime do ASP.NET Core quando o aplicativo é iniciado:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

O exemplo anterior é para [Razor Pages](xref:razor-pages/index); a versão MVC é semelhante.


A classe `Startup` é especificada quando o [host](xref:fundamentals/index#host) do aplicativo é criado. A `Startup` classe é tipicamente especificada chamando o [Método\<WebHostBuilderExtensions.UseStartup TStartup>no](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) construtor de host:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

O host fornece serviços que estão disponíveis para o construtor de classe `Startup`. O aplicativo adiciona serviços adicionais por meio de `ConfigureServices`. Os serviços de aplicativos e o host ficam disponíveis em `Configure` e em todo o aplicativo.

Somente os seguintes tipos de `Startup` serviço podem ser injetados<xref:Microsoft.Extensions.Hosting.IHostBuilder>no construtor ao usar o Host [Genérico](xref:fundamentals/host/generic-host) ( ):

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

A maioria dos serviços não está disponível até o método `Configure` ser chamado.

### <a name="multiple-startup"></a>Inicialização múltipla

Quando o aplicativo define classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`), a classe `Startup` apropriada é selecionada no runtime. A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada. Se o aplicativo for executado no ambiente de desenvolvimento e incluir uma classe `Startup` e uma classe `StartupDevelopment`, a classe `StartupDevelopment` será usada. Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Veja [O host](xref:fundamentals/index#host) para obter mais informações sobre o host. Para obter informações sobre como tratar erros durante a inicialização, consulte [Tratamento de exceção na inicialização](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>O método ConfigureServices

O método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> é:

* Opcional.
* Chamado pelo host antes do método `Configure` para configurar os serviços do aplicativo.
* Quando as [opções de configuração](xref:fundamentals/configuration/index) são definidas por convenção.

O host pode configurar alguns serviços antes que métodos `Startup` sejam chamados. Para obter mais informações, confira [O host](xref:fundamentals/index#host).

Para recursos que exigem uma configuração significativa, há métodos de extensão `Add{Service}` em <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Por exemplo, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores e **Add**RazorPages:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

Adicionar serviços ao contêiner de serviços os torna disponíveis dentro do aplicativo e no método `Configure`. Os serviços são resolvidos por meio da [injeção de dependência](xref:fundamentals/dependency-injection) ou de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

## <a name="the-configure-method"></a>O método Configure

O método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> é usado para especificar como o aplicativo responde às solicitações HTTP. O pipeline de solicitação é configurado adicionando componentes de [middleware](xref:fundamentals/middleware/index) a uma instância de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` está disponível para o método `Configure`, mas não é registrado no contêiner de serviço. A hospedagem cria um `IApplicationBuilder` e o passa diretamente para `Configure`.

Os [modelos do ASP.NET Core](/dotnet/core/tools/dotnet-new) configuram o pipeline com suporte para:

* [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page)
* [Manipulador de exceção](xref:fundamentals/error-handling#exception-handler-page)
* [Segurança de Transporte Estrita de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Redirecionamento de HTTPS](xref:security/enforcing-ssl)
* [Arquivos estáticos](xref:fundamentals/static-files)
* [MVC](xref:mvc/overview) do ASP.NET Core e [Razor Pages](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

O exemplo anterior é para [Razor Pages](xref:razor-pages/index); a versão MVC é semelhante.

Cada método de extensão `Use` adiciona um ou mais componentes de middleware ao pipeline de solicitação. Por exemplo, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configura o [middleware](xref:fundamentals/middleware/index) para atender [arquivos estáticos](xref:fundamentals/static-files).

Cada componente de middleware no pipeline de solicitação é responsável por invocar o próximo componente no pipeline ou causar um curto-circuito da cadeia, se apropriado.

Serviços adicionais, como `IWebHostEnvironment`, `ILoggerFactory` ou qualquer coisa definida em `ConfigureServices`, podem ser especificados na assinatura do método `Configure`. Esses serviços serão injetados se estiverem disponíveis.

Para obter mais informações de como usar o `IApplicationBuilder` e a ordem de processamento de middleware, confira <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Configurar serviços sem Inicialização

Para configurar serviços e o pipeline de processamento de solicitação sem usar uma classe `Startup`, chame os métodos de conveniência `ConfigureServices` e `Configure` no construtor do host. Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras. Se houver várias chamadas de método `Configure`, a última chamada de `Configure` será usada.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Estender a inicialização com filtros de inicialização

Use: <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>

* Para configurar middleware no início ou no final do pipeline [configure](#the-configure-method) middleware de um aplicativo sem uma chamada explícita para `Use{Middleware}`. `IStartupFilter`é usado por ASP.NET Core para adicionar padrões ao início do pipeline sem ter que fazer com que o autor do aplicativo registre explicitamente o middleware padrão. `IStartupFilter`permite uma chamada `Use{Middleware}` de componente diferente em nome do autor do aplicativo.
* Para criar um `Configure` pipeline de métodos. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) pode definir um middleware para ser executado antes ou depois do middleware adicionado pelas bibliotecas.

`IStartupFilter` implementa <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, que recebe e retorna um `Action<IApplicationBuilder>`. Um <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> define uma classe para configurar o pipeline de solicitação do aplicativo. Para obter mais informações, confira [Criar um pipeline de middleware com o IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Cada `IStartupFilter` pode adicionar um ou mais middlewares no pipeline de solicitação. Os filtros são invocados na ordem em que foram adicionados ao contêiner de serviço. Filtros podem adicionar middleware antes ou depois de passar o controle para o filtro seguinte, de modo que eles são acrescentados ao início ou no final do pipeline de aplicativo.

O exemplo a seguir demonstra como registrar um middleware com `IStartupFilter`. O middleware `RequestSetOptionsMiddleware` define um valor de opção de um parâmetro de cadeia de caracteres de consulta:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

O `RequestSetOptionsMiddleware` é configurado na classe `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

O `IStartupFilter` é registrado no contêiner de serviço em <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Quando um parâmetro de cadeia de caracteres de consulta para `option` é fornecido, o middleware processa a atribuição de valor antes que o middleware do ASP.NET Core renderize a resposta.

A ordem de execução do middleware é definida pela ordem dos registros `IStartupFilter`:

* Várias implementações de `IStartupFilter` podem interagir com os mesmos objetos. Se a ordem for importante, ordene seus registros de serviço `IStartupFilter` para corresponder à ordem em que os middlewares devem ser executados.
* Bibliotecas podem adicionar middleware com uma ou mais implementações de `IStartupFilter` que são executadas antes ou depois de outro middleware de aplicativo registrado com `IStartupFilter`. Para invocar um middleware `IStartupFilter` antes de um middleware adicionado pelo `IStartupFilter` de uma biblioteca:

  * Posicione o registro do serviço antes que a biblioteca seja adicionada ao contêiner de serviço.
  * Para invocá-lo posteriormente, posicione o registro do serviço após a biblioteca ser adicionada.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Adicionar configuração na inicialização usando um assembly externo

Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo. Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Recursos adicionais

* [O host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>A classe Startup

Os aplicativos do ASP.NET Core usam uma classe `Startup`, que é chamada de `Startup` por convenção. A classe `Startup`:

* Opcionalmente, inclua um método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> para configurar os *serviços* do aplicativo. Um serviço é um componente reutilizável que fornece a funcionalidade do aplicativo. Os serviços `ConfigureServices` são registrados e *consumidos* em todo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>o aplicativo via injeção de [dependência (DI)](xref:fundamentals/dependency-injection) ou .
* Inclui um método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> para criar o pipeline de processamento de solicitações do aplicativo.

`ConfigureServices` e `Configure` são chamados pelo runtime do ASP.NET Core quando o aplicativo é iniciado:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

A classe `Startup` é especificada quando o [host](xref:fundamentals/index#host) do aplicativo é criado. A `Startup` classe é tipicamente especificada chamando o [Método\<WebHostBuilderExtensions.UseStartup TStartup>no](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) construtor de host:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

O host fornece serviços que estão disponíveis para o construtor de classe `Startup`. O aplicativo adiciona serviços adicionais por meio de `ConfigureServices`. Os serviços de aplicativos e o host ficam, então, disponíveis em `Configure` e em todo o aplicativo.

Um uso comum da [injeção de dependência](xref:fundamentals/dependency-injection) na classe `Startup` é injetar:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para configurar serviços pelo ambiente.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> para ler a configuração.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> para criar um agente em `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

A maioria dos serviços não está disponível até o método `Configure` ser chamado.

### <a name="multiple-startup"></a>Inicialização múltipla

Quando o aplicativo define classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`), a classe `Startup` apropriada é selecionada no runtime. A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada. Se o aplicativo for executado no ambiente de desenvolvimento e incluir uma classe `Startup` e uma classe `StartupDevelopment`, a classe `StartupDevelopment` será usada. Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Veja [O host](xref:fundamentals/index#host) para obter mais informações sobre o host. Para obter informações sobre como tratar erros durante a inicialização, consulte [Tratamento de exceção na inicialização](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>O método ConfigureServices

O método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> é:

* Opcional.
* Chamado pelo host antes do método `Configure` para configurar os serviços do aplicativo.
* Quando as [opções de configuração](xref:fundamentals/configuration/index) são definidas por convenção.

O host pode configurar alguns serviços antes que métodos `Startup` sejam chamados. Para obter mais informações, confira [O host](xref:fundamentals/index#host).

Para recursos que exigem uma configuração significativa, há métodos de extensão `Add{Service}` em <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Por exemplo, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores e **Add**RazorPages:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Adicionar serviços ao contêiner de serviços os torna disponíveis dentro do aplicativo e no método `Configure`. Os serviços são resolvidos por meio da [injeção de dependência](xref:fundamentals/dependency-injection) ou de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

Confira [SetCompatibilityVersion](xref:mvc/compatibility-version) para obter mais informações sobre `SetCompatibilityVersion`.

## <a name="the-configure-method"></a>O método Configure

O método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> é usado para especificar como o aplicativo responde às solicitações HTTP. O pipeline de solicitação é configurado adicionando componentes de [middleware](xref:fundamentals/middleware/index) a uma instância de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` está disponível para o método `Configure`, mas não é registrado no contêiner de serviço. A hospedagem cria um `IApplicationBuilder` e o passa diretamente para `Configure`.

Os [modelos do ASP.NET Core](/dotnet/core/tools/dotnet-new) configuram o pipeline com suporte para:

* [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page)
* [Manipulador de exceção](xref:fundamentals/error-handling#exception-handler-page)
* [Segurança de Transporte Estrita de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Redirecionamento de HTTPS](xref:security/enforcing-ssl)
* [Arquivos estáticos](xref:fundamentals/static-files)
* [MVC](xref:mvc/overview) do ASP.NET Core e [Razor Pages](xref:razor-pages/index)
* [Regulamentação de Proteção Geral de Dados (GDPR)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Cada método de extensão `Use` adiciona um ou mais componentes de middleware ao pipeline de solicitação. Por exemplo, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configura o [middleware](xref:fundamentals/middleware/index) para atender [arquivos estáticos](xref:fundamentals/static-files).

Cada componente de middleware no pipeline de solicitação é responsável por invocar o próximo componente no pipeline ou causar um curto-circuito da cadeia, se apropriado.

Serviços adicionais, como `IHostingEnvironment` e `ILoggerFactory` ou qualquer coisa definida em `ConfigureServices`, podem ser especificados na assinatura do método `Configure`. Esses serviços serão injetados se estiverem disponíveis.

Para obter mais informações de como usar o `IApplicationBuilder` e a ordem de processamento de middleware, confira <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Configurar serviços sem Inicialização

Para configurar serviços e o pipeline de processamento de solicitação sem usar uma classe `Startup`, chame os métodos de conveniência `ConfigureServices` e `Configure` no construtor do host. Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras. Se houver várias chamadas de método `Configure`, a última chamada de `Configure` será usada.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Estender a inicialização com filtros de inicialização

Use: <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>

* Para configurar middleware no início ou no final do pipeline [configure](#the-configure-method) middleware de um aplicativo sem uma chamada explícita para `Use{Middleware}`. `IStartupFilter`é usado por ASP.NET Core para adicionar padrões ao início do pipeline sem ter que fazer com que o autor do aplicativo registre explicitamente o middleware padrão. `IStartupFilter`permite uma chamada `Use{Middleware}` de componente diferente em nome do autor do aplicativo.
* Para criar um `Configure` pipeline de métodos. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) pode definir um middleware para ser executado antes ou depois do middleware adicionado pelas bibliotecas.

`IStartupFilter` implementa <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, que recebe e retorna um `Action<IApplicationBuilder>`. Um <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> define uma classe para configurar o pipeline de solicitação do aplicativo. Para obter mais informações, confira [Criar um pipeline de middleware com o IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Cada `IStartupFilter` pode adicionar um ou mais middlewares no pipeline de solicitação. Os filtros são invocados na ordem em que foram adicionados ao contêiner de serviço. Filtros podem adicionar middleware antes ou depois de passar o controle para o filtro seguinte, de modo que eles são acrescentados ao início ou no final do pipeline de aplicativo.

O exemplo a seguir demonstra como registrar um middleware com `IStartupFilter`. O middleware `RequestSetOptionsMiddleware` define um valor de opção de um parâmetro de cadeia de caracteres de consulta:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

O `RequestSetOptionsMiddleware` é configurado na classe `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

O `IStartupFilter` é registrado no contêiner de serviço em <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Quando um parâmetro de cadeia de caracteres de consulta para `option` é fornecido, o middleware processa a atribuição de valor antes que o middleware do ASP.NET Core renderize a resposta.

A ordem de execução do middleware é definida pela ordem dos registros `IStartupFilter`:

* Várias implementações de `IStartupFilter` podem interagir com os mesmos objetos. Se a ordem for importante, ordene seus registros de serviço `IStartupFilter` para corresponder à ordem em que os middlewares devem ser executados.
* Bibliotecas podem adicionar middleware com uma ou mais implementações de `IStartupFilter` que são executadas antes ou depois de outro middleware de aplicativo registrado com `IStartupFilter`. Para invocar um middleware `IStartupFilter` antes de um middleware adicionado pelo `IStartupFilter` de uma biblioteca:

  * Posicione o registro do serviço antes que a biblioteca seja adicionada ao contêiner de serviço.
  * Para invocá-lo posteriormente, posicione o registro do serviço após a biblioteca ser adicionada.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Adicionar configuração na inicialização usando um assembly externo

Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo. Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Recursos adicionais

* [O host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end