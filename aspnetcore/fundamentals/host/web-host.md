---
title: Host da Web do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o host da Web no ASP.NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: e02d6efcb3aec1329469b8654e66ba845870421a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666708"
---
# <a name="aspnet-core-web-host"></a>Host da Web do ASP.NET Core

ASP.NET aplicativos Core configurem e iniciem um *host*. O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo. No mínimo, o host configura um servidor e um pipeline de processamento de solicitações. O host também pode configurar registro em log, a injeção de dependência e a configuração.

::: moniker range=">= aspnetcore-3.0"

Este artigo aborda o host da Web, que permanece disponível somente para compatibilidade com versões anteriores. O [Host genérico](xref:fundamentals/host/generic-host) é recomendado para todos os tipos de aplicativo.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este artigo aborda o host da Web, que serve para hospedar aplicativos Web. Para outros tipos de aplicativos, use o [Host genérico](xref:fundamentals/host/generic-host).

::: moniker-end

## <a name="set-up-a-host"></a>Configurar um host

Crie um host usando uma instância do [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Normalmente, isso é feito no ponto de entrada do aplicativo, o método `Main`.

Nos modelos de `Main` projeto, está localizado em *Program.cs*. Um aplicativo típico chama [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para começar a configurar um host:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

O código que chama `CreateDefaultBuilder` está em um método chamado `CreateWebHostBuilder`, que o separa do código em `Main`, que chama `Run` no objeto de construtor. Essa separação será necessária se você usar [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/). As ferramentas esperam encontrar um método `CreateWebHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo. Uma alternativa é implementar `IDesignTimeDbContextFactory`. Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).

`CreateDefaultBuilder` realiza as seguintes tarefas:

* Configura o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web usando provedores de configuração de hospedagem do aplicativo. Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.
* Define a raiz de [conteúdo](xref:fundamentals/index#content-root) para o caminho retornado pelo [Diretório.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Carrega a [configuração do host](#host-configuration-values) de:
  * Variáveis de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`).
  * Argumentos de linha de comando.
* Carrega a configuração do aplicativo na seguinte ordem de:
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * [Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.
  * Variáveis de ambiente.
  * Argumentos de linha de comando.
* Configura o [registro em log](xref:fundamentals/logging/index) para a saída do console e de depuração. O registro em log inclui regras de [filtragem de log](xref:fundamentals/logging/index#log-filtering) especificadas em uma seção de configuração de registro em log de um arquivo *appsettings.json* ou *appsettings.{Environment}.json*.
* Ao correr atrás do IIS `CreateDefaultBuilder` com o ASP.NET Módulo [Central,](xref:host-and-deploy/aspnet-core-module)habilita a [Integração IIS,](xref:host-and-deploy/iis/index)que configura o endereço base e a porta do aplicativo. A Integração do IIS também configura o aplicativo para [capturar erros de inicialização](#capture-startup-errors). Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.
* Definirá [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` se o ambiente do aplicativo for de desenvolvimento. Para obter mais informações, confira [Validação de escopo](#scope-validation).

A configuração definida por `CreateDefaultBuilder` pode ser substituída e aumentada por [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) e outros métodos, bem como os métodos de extensão de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Veja a seguir alguns exemplos:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) é usado para especificar `IConfiguration` adicionais para o aplicativo. A seguinte chamada de `ConfigureAppConfiguration` adiciona um delegado para incluir a configuração do aplicativo no arquivo *appsettings.xml*. `ConfigureAppConfiguration` pode ser chamado várias vezes. Observe que essa configuração não se aplica ao host (por exemplo, URLs de servidor ou de ambiente). Consulte a seção [Valores de configuração de Host](#host-configuration-values).

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* A seguinte chamada de `ConfigureLogging` adiciona um delegado para configurar o nível de log mínimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) como [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel). Esta configuração substitui as configurações em *configurações. Development.json* `LogLevel.Debug`( ) e *appsettings. Production.json* `LogLevel.Error`( ) `CreateDefaultBuilder`configurado por . `ConfigureLogging` pode ser chamado várias vezes.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* A seguinte chamada para `ConfigureKestrel` substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 milhões de bytes, estabelecido quando o Kestrel foi configurado pelo `CreateDefaultBuilder`:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* A seguinte chamada a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30.000.000 bytes estabelecido quando Kestrel foi configurado por `CreateDefaultBuilder`:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

A [raiz do conteúdo](xref:fundamentals/index#content-root) determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC. Quando o aplicativo é iniciado na pasta raiz do projeto, essa pasta é usada como a raiz do conteúdo. Esse é o padrão usado no [Visual Studio](https://visualstudio.microsoft.com) e nos [novos modelos dotnet](/dotnet/core/tools/dotnet-new).

Para obter mais informações sobre a configuração de aplicativo, veja <xref:fundamentals/configuration/index>.

> [!NOTE]
> Como uma alternativa ao uso do método `CreateDefaultBuilder` estático, criar um host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) é uma abordagem compatível com o ASP.NET Core 2. x.

Ao configurar um host, os métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) podem ser fornecidos. Se uma classe `Startup` for especificada, ela deverá definir um método `Configure`. Para obter mais informações, consulte <xref:fundamentals/startup>. Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras. Diversas chamadas para `Configure` ou `UseStartup` no `WebHostBuilder` substituem configurações anteriores.

## <a name="host-configuration-values"></a>Valores de configuração do host

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) conta com as seguintes abordagens para definir os valores de configuração do host:

* Configuração do construtor do host, que inclui variáveis de ambiente com o formato `ASPNETCORE_{configurationKey}`. Por exemplo, `ASPNETCORE_ENVIRONMENT`.
* Extensões como [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) e [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (consulte a seção [Configuração de substituição](#override-configuration)).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) e a chave associada. Ao definir um valor com `UseSetting`, o valor é definido como uma cadeia de caracteres, independentemente do tipo.

O host usa a opção que define um valor por último. Para obter mais informações, veja [Substituir configuração](#override-configuration) na próxima seção.

### <a name="application-key-name"></a>Chave do Aplicativo (Nome)

::: moniker range=">= aspnetcore-3.0"

A `IWebHostEnvironment.ApplicationName` propriedade é definida automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) é chamado durante a construção do host. O valor é definido para o nome do assembly que contém o ponto de entrada do aplicativo. Para definir o valor explicitamente, use o [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

::: moniker-end

::: moniker range="< aspnetcore-3.0"

A propriedade [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) é definida automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) é chamado durante a construção do host. O valor é definido para o nome do assembly que contém o ponto de entrada do aplicativo. Para definir o valor explicitamente, use o [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

::: moniker-end

**Chave**: applicationName  
**Tipo:** *string*  
**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.  
**Conjunto usando:**`UseSetting`  
**Variável de ambiente:**`ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>Capturar erros de inicialização

Esta configuração controla a captura de erros de inicialização.

**Chave**: captureStartupErrors  
**Tipo**: *bool* (`true` ou `1`)  
**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.  
**Conjunto usando:**`CaptureStartupErrors`  
**Variável de ambiente:**`ASPNETCORE_CAPTURESTARTUPERRORS`

Quando `false`, erros durante a inicialização resultam no encerramento do host. Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>Raiz do conteúdo

Esta configuração determina onde ASP.NET Core começa a procurar arquivos de conteúdo.

**Chave**: contentRoot  
**Tipo:** *string*  
**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.  
**Conjunto usando:**`UseContentRoot`  
**Variável de ambiente:**`ASPNETCORE_CONTENTROOT`

A raiz de conteúdo também é usada como o caminho base para a [raiz da Web](xref:fundamentals/index#web-root). Se o caminho raiz de conteúdo não existir, o host não será inicializar.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

Para obter mais informações, consulte:

* [Fundamentos: Raiz de conteúdo](xref:fundamentals/index#content-root)
* [Raiz da Web](#web-root)

### <a name="detailed-errors"></a>Erros detalhados

Determina se erros detalhados devem ser capturados.

**Chave**: detailedErrors  
**Tipo**: *bool* (`true` ou `1`)  
**Padrão**: falso  
**Conjunto usando:**`UseSetting`  
**Variável de ambiente:**`ASPNETCORE_DETAILEDERRORS`

Quando habilitado (ou quando o <a href="#environment">Ambiente</a> é definido como `Development`), o aplicativo captura exceções detalhadas.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>Ambiente

Define o ambiente do aplicativo.

**Chave**: ambiente  
**Tipo:** *string*  
**Padrão**: Production  
**Conjunto usando:**`UseEnvironment`  
**Variável de ambiente:**`ASPNETCORE_ENVIRONMENT`

O ambiente pode ser definido como qualquer valor. Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`. Os valores não diferenciam maiúsculas de minúsculas. Por padrão, o *Ambiente* é lido da variável de ambiente `ASPNETCORE_ENVIRONMENT`. Ao usar o [Visual Studio](https://visualstudio.microsoft.com), variáveis de ambiente podem ser definidas no arquivo *launchSettings.json*. Para obter mais informações, consulte <xref:fundamentals/environments>.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>Hospedando assemblies de inicialização

Define os assemblies de inicialização de hospedagem do aplicativo.

**Chave**: hostingStartupAssemblies  
**Tipo:** *string*  
**Padrão**: Cadeia de seqüência vazia  
**Conjunto usando:**`UseSetting`  
**Variável de ambiente:**`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.

Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo. Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>Porta HTTPS

Defina a porta de redirecionamento HTTPS. Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).

**Chave**: https_port **Tipo**: *string*
**Padrão**: Um valor padrão não está definido.
**Conjunto usando:** `UseSetting` 
 **Variável de ambiente:**`ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>Hospedando assemblies de exclusão de inicialização

Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.

**Chave**: hostingStartupExcludeAssemblies  
**Tipo:** *string*  
**Padrão**: Cadeia de seqüência vazia  
**Conjunto usando:**`UseSetting`  
**Variável de ambiente:**`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>Preferir URLs de hospedagem

Indica se o host deve escutar as URLs configuradas com o `WebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.

**Chave**: preferHostingUrls  
**Tipo**: *bool* (`true` ou `1`)  
**Padrão:** verdadeiro  
**Conjunto usando:**`PreferHostingUrls`  
**Variável de ambiente:**`ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>Impedir inicialização de hospedagem

Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo. Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.

**Chave**: preventHostingStartup  
**Tipo**: *bool* (`true` ou `1`)  
**Padrão**: falso  
**Conjunto usando:**`UseSetting`  
**Variável de ambiente:**`ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>URLs de servidor

Indica os endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.

**Chave**: urls  
**Tipo:** *string*  
**Padrão:**http://localhost:5000  
**Conjunto usando:**`UseUrls`  
**Variável de ambiente:**`ASPNETCORE_URLS`

Defina como uma lista separada por ponto e vírgula (;) de prefixos de URL aos quais o servidor deve responder. Por exemplo, `http://localhost:123`. Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`). O protocolo (`http://` ou `https://`) deve ser incluído com cada URL. Os formatos compatíveis variam dependendo dos servidores.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

O Kestrel tem sua própria API de configuração de ponto de extremidade. Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="shutdown-timeout"></a>Tempo limite de desligamento

Especifica o tempo de espera para o desligamento do host da Web.

**Chave**: shutdownTimeoutSeconds  
**Tipo**: *int*  
**Padrão**: 5  
**Conjunto usando:**`UseShutdownTimeout`  
**Variável de ambiente:**`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

Embora a chave aceite um *int* com `UseSetting` (por exemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), o método de extensão [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) usa um [TimeSpan](/dotnet/api/system.timespan).

Durante o período de tempo limite, a hospedagem:

* Dispara [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.

Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado. Os serviços serão parados mesmo se ainda não tiverem concluído o processamento. Se os serviços exigirem mais tempo para parar, aumente o tempo limite.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>Assembly de inicialização

Determina o assembly para pesquisar pela classe `Startup`.

**Chave**: startupAssembly  
**Tipo:** *string*  
**Padrão**: o assembly do aplicativo  
**Conjunto usando:**`UseStartup`  
**Variável de ambiente:**`ASPNETCORE_STARTUPASSEMBLY`

O assembly por nome (`string`) ou por tipo (`TStartup`) pode ser referenciado. Se vários métodos `UseStartup` forem chamados, o último terá precedência.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Raiz da Web

Define o caminho relativo para os ativos estáticos do aplicativo.

**Chave**: webroot  
**Tipo:** *string*  
**Padrão**: O `wwwroot`padrão é . O caminho para *{content root}/wwwroot* deve existir. Se o caminho não existir, um provedor de arquivo não operacional será usado.  
**Conjunto usando:**`UseWebRoot`  
**Variável de ambiente:**`ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

Para obter mais informações, consulte:

* [Fundamentos: Raiz da Web](xref:fundamentals/index#web-root)
* [Raiz do conteúdo](#content-root)

## <a name="override-configuration"></a>Substituir configuração

Use [Configuração](xref:fundamentals/configuration/index) para configurar o host da Web. No exemplo a seguir, a configuração do host é especificada, opcionalmente, em um arquivo *hostsettings.json*. Qualquer configuração carregada do arquivo *hostsettings.json* pode ser substituída por argumentos de linha de comando. A configuração de build (no `config`) é usada para configurar o host com [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration). A configuração `IWebHostBuilder` é adicionada à configuração do aplicativo, mas o contrário não é verdade&mdash;`ConfigureAppConfiguration` não afeta a configuração `IWebHostBuilder`.

Substituição da configuração fornecida por `UseUrls` pela configuração de *hostsettings.json* primeiro, e pela configuração de argumento da linha de comando depois:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

*hostsettings.json*:

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> [UsoConfiguração](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) apenas copia chaves `IConfiguration` da configuração do construtor de host. Portanto, definir `reloadOnChange: true` para arquivos de configuração JSON, INI e XML não tem nenhum efeito.

Para especificar o host executado em uma URL específica, o valor desejado pode ser passado em um prompt de comando ao executar [dotnet run](/dotnet/core/tools/dotnet-run). O argumento de linha de comando substitui o valor `urls` do arquivo *hostsettings.json* e o servidor escuta na porta 8080:

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Gerenciar o host

**Executar**

O método `Run` inicia o aplicativo Web e bloqueia o thread de chamada até que o host seja desligado:

```csharp
host.Run();
```

**Início**

Execute o host sem bloqueio, chamando seu método `Start`:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Se uma lista de URLs for passada para o método `Start`, ele escutará nas URLs especificadas:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

O aplicativo pode inicializar e iniciar um novo host usando os padrões pré-configurados de `CreateDefaultBuilder`, usando um método estático conveniente. Esses métodos iniciam o servidor sem uma saída do console e com [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) e aguardam uma quebra (Ctrl-C/SIGINT ou SIGTERM):

**Start(RequestDelegate app)**

Inicie com um `RequestDelegate`:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!" `WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida. O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.

**Start(string url, RequestDelegate app)**

Inicie com uma URL e `RequestDelegate`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Produz o mesmo resultado que **Start(RequestDelegate app)**, mas o aplicativo responde em `http://localhost:8080`.

**Start(Action\<IRouteBuilder> routeBuilder)**

Use uma instância de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar o middleware de roteamento:

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Use as seguintes solicitações de navegador com o exemplo:

| Solicitação                                    | Resposta                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Hello, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Gera uma exceção com a cadeia de caracteres "ooops!" |
| `http://localhost:5000/throw`              | Gera uma exceção com a cadeia de caracteres "Uh oh!" |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevin!                            |
| `http://localhost:5000`                    | Olá, Mundo!                             |

`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida. O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.

**Start(url de\<seqüência, Action IRouteBuilder> routeBuilder)**

Use uma URL e uma instância de `IRouteBuilder`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Produz o mesmo resultado **que\<Start (Action IRouteBuilder> routeBuilder)**, exceto que o aplicativo responde em `http://localhost:8080`.

**StartWith (Aplicativo action\<iapplicationbuilder>)**

Forneça um delegado para configurar um `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!" `WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida. O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.

**StartWith (url de\<seqüência, Action IApplicationBuilder> app)**

Forneça um delegado e uma URL para configurar um `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Produz o mesmo resultado que **O Aplicativo StartWith (Action\<IApplicationBuilder>),** exceto que o aplicativo responde em `http://localhost:8080`.

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a>Interface iWebHostAmbiente

A `IWebHostEnvironment` interface fornece informações sobre o ambiente de hospedagem web do aplicativo. Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IWebHostEnvironment` para usar suas propriedades e métodos de extensão:

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

Uma [abordagem baseada em convenção](xref:fundamentals/environments#environment-based-startup-class-and-methods) pode ser usada para configurar o aplicativo na inicialização com base no ambiente. Como alternativa, injete o `IWebHostEnvironment` no construtor `Startup` para uso em `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Além do método de extensão `IsDevelopment`, `IWebHostEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`. Para obter mais informações, consulte <xref:fundamentals/environments>.

O serviço `IWebHostEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IWebHostEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/write) personalizado:

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a>Interface IHostingEnvironment

A [interface IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fornece informações sobre o ambiente de hospedagem na Web do aplicativo. Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IHostingEnvironment` para usar suas propriedades e métodos de extensão:

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

Uma [abordagem baseada em convenção](xref:fundamentals/environments#environment-based-startup-class-and-methods) pode ser usada para configurar o aplicativo na inicialização com base no ambiente. Como alternativa, injete o `IHostingEnvironment` no construtor `Startup` para uso em `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Além do método de extensão `IsDevelopment`, `IHostingEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`. Para obter mais informações, consulte <xref:fundamentals/environments>.

O serviço `IHostingEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IHostingEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/write) personalizado:

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a>Interface IHostApplicationLifetime

`IHostApplicationLifetime`permite atividades pós-inicialização e desligamento. Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.

| Token de cancelamento    | Acionado quando&#8230; |
| --------------------- | --------------------- |
| `ApplicationStarted`  | O host foi iniciado totalmente. |
| `ApplicationStopped`  | O host está concluindo um desligamento normal. Todas as solicitações devem ser processadas. O desligamento é bloqueado até que esse evento seja concluído. |
| `ApplicationStopping` | O host está executando um desligamento normal. Solicitações ainda podem estar sendo processadas. O desligamento é bloqueado até que esse evento seja concluído. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

`StopApplication` solicita o término do aplicativo. A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a>Interface IApplicationLifetime

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite atividades pós-inicialização e desligamento. Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.

| Token de cancelamento    | Acionado quando&#8230; |
| --------------------- | --------------------- |
| [ApplicationStarted](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | O host foi iniciado totalmente. |
| [ApplicationStopped](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | O host está concluindo um desligamento normal. Todas as solicitações devem ser processadas. O desligamento é bloqueado até que esse evento seja concluído. |
| [ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | O host está executando um desligamento normal. Solicitações ainda podem estar sendo processadas. O desligamento é bloqueado até que esse evento seja concluído. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita o término do aplicativo. A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="scope-validation"></a>Validação de escopo

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) define [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` quando o ambiente do aplicativo é de desenvolvimento.

Quando `ValidateScopes` está definido como `true`, o provedor de serviço padrão executa verificações para saber se:

* Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.
* Os serviços com escopo não são injetados direta ou indiretamente em singletons.

O provedor de serviços raiz é criado quando [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) é chamado. O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.

Os serviços com escopo são descartados pelo contêiner que os criou. Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado. A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.

Para que os escopos sempre sejam validados, incluindo no ambiente de produção, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) com [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) no construtor do host:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
