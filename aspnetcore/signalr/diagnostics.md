---
title: Registro em log e diagnóstico no ASP.NET Core SignalR
author: anurse
description: Saiba como coletar diagnósticos do seu SignalR aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr
ms.date: 06/12/2020
no-loc:
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
uid: signalr/diagnostics
ms.openlocfilehash: 649398a3868117b2e7f3358aa25544c99cc625b3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631336"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a>Registro em log e diagnóstico no ASP.NET Core SignalR

Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)

Este artigo fornece orientação para a coleta de diagnósticos do seu SignalR aplicativo ASP.NET Core para ajudar a solucionar problemas.

## <a name="server-side-logging"></a>Registro em log do lado do servidor

> [!WARNING]
> Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo. **Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.

Como SignalR o faz parte do ASP.NET Core, ele usa o sistema de registro em log de ASP.NET Core. Na configuração padrão, o SignalR registra informações muito poucas, mas isso pode ser configurado. Consulte a documentação em [log de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o log de ASP.NET Core.

SignalR usa duas categorias de agente:

* `Microsoft.AspNetCore.SignalR`: Para logs relacionados a protocolos de Hub, ativação de hubs, invocação de métodos e outras atividades relacionadas ao Hub.
* `Microsoft.AspNetCore.Http.Connections`: Para logs relacionados a transportes, como WebSockets, sondagem longa, eventos enviados pelo servidor e infraestrutura de nível baixo SignalR .

Para habilitar logs detalhados do SignalR , configure os dois prefixos anteriores para o `Debug` nível em seu *appsettings.jsno* arquivo adicionando os seguintes itens à `LogLevel` subseção em `Logging` :

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

Você também pode configurar isso no código em seu `CreateWebHostBuilder` método:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Se você não estiver usando a configuração baseada em JSON, defina os seguintes valores de configuração em seu sistema de configuração:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados. Por exemplo, ao usar variáveis de ambiente, dois `_` caracteres são usados em vez do `:` (por exemplo, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).

É recomendável usar o `Debug` nível ao coletar diagnósticos mais detalhados para seu aplicativo. O `Trace` nível produz diagnósticos de nível muito baixo e raramente é necessário para diagnosticar problemas em seu aplicativo.

## <a name="access-server-side-logs"></a>Acessar logs do lado do servidor

A maneira como você acessa os logs do lado do servidor depende do ambiente no qual você está executando o.

### <a name="as-a-console-app-outside-iis"></a>Como um aplicativo de console fora do IIS

Se você estiver executando o em um aplicativo de console, o [agente de log do console](xref:fundamentals/logging/index#console) deverá ser habilitado por padrão. SignalR os logs serão exibidos no console do.

### <a name="within-iis-express-from-visual-studio"></a>Dentro de IIS Express do Visual Studio

O Visual Studio exibe a saída de log na janela **saída** . Selecione a opção de menu suspenso do **ASP.NET Core Web Server** .

### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

Habilite a opção de **log do aplicativo (Filesystem)** na seção **logs de diagnóstico** do portal do serviço Azure app e configure o **nível** como `Verbose` . Os logs devem estar disponíveis no serviço de **streaming de log** e nos logs no sistema de arquivos do serviço de aplicativo. Para obter mais informações, consulte [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Outros ambientes

Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, kubernetes ou serviço do Windows), consulte <xref:fundamentals/logging/index> para obter mais informações sobre como configurar provedores de log adequados para o ambiente.

## <a name="javascript-client-logging"></a>Log de cliente JavaScript

> [!WARNING]
> Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo. **Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.

Ao usar o cliente JavaScript, você pode configurar as opções de log usando o `configureLogging` método em `HubConnectionBuilder` :

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.

A tabela a seguir mostra os níveis de log disponíveis para o cliente JavaScript. Definir o nível de log como um desses valores habilita o registro em log nesse nível e todos os níveis acima dele na tabela.

| Nível | Descrição |
| ----- | ----------- |
| `None` | Nenhuma mensagem é registrada em log. |
| `Critical` | Mensagens que indicam uma falha em todo o aplicativo. |
| `Error` | Mensagens que indicam uma falha na operação atual. |
| `Warning` | Mensagens que indicam um problema não fatal. |
| `Information` | Mensagens informativas. |
| `Debug` | Mensagens de diagnóstico úteis para depuração. |
| `Trace` | Mensagens de diagnóstico muito detalhadas projetadas para diagnosticar problemas específicos. |

Depois de configurar o detalhamento, os logs serão gravados no console do navegador (ou na saída padrão em um aplicativo NodeJS).

Se você quiser enviar logs para um sistema de registro em log personalizado, poderá fornecer um objeto JavaScript que implementa a `ILogger` interface. O único método que precisa ser implementado é `log` , que usa o nível do evento e a mensagem associada ao evento. Por exemplo:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>Log de cliente .NET

> [!WARNING]
> Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo. **Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.

Para obter logs do cliente .NET, você pode usar o `ConfigureLogging` método em `HubConnectionBuilder` . Isso funciona da mesma maneira que o `ConfigureLogging` método em `WebHostBuilder` e `HostBuilder` . Você pode configurar os mesmos provedores de log usados no ASP.NET Core. No entanto, você precisa instalar e habilitar manualmente os pacotes NuGet para os provedores de log individuais.

Para adicionar o log de cliente .NET a um Blazor WebAssembly aplicativo, consulte <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> .

### <a name="console-logging"></a>Log de console

Para habilitar o log do console, adicione o pacote [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) . Em seguida, use o `AddConsole` método para configurar o agente de log do console:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Depurar log da janela de saída

Você também pode configurar logs para ir para a janela de **saída** no Visual Studio. Instale o pacote [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) e use o `AddDebug` método:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Outros provedores de log

SignalR dá suporte a outros provedores de log, como Serilog, Seq, NLog ou qualquer outro sistema de registro em log que se integre ao `Microsoft.Extensions.Logging` . Se o sistema de registro em log fornecer um `ILoggerProvider` , você poderá registrá-lo com `AddProvider` :

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Detalhes do controle

Se você estiver fazendo logon de outros locais em seu aplicativo, alterar o nível padrão para `Debug` pode ser muito detalhado. Você pode usar um filtro para configurar o nível de log para SignalR logs. Isso pode ser feito no código, praticamente da mesma forma que no servidor:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Rastreamentos de rede

> [!WARNING]
> Um rastreamento de rede contém todo o conteúdo de cada mensagem enviada pelo seu aplicativo. **Nunca** poste rastreamentos de rede brutos de aplicativos de produção para fóruns públicos, como o github.

Se você encontrar um problema, um rastreamento de rede pode, às vezes, fornecer muitas informações úteis. Isso é particularmente útil se você pretende arquivar um problema em nosso Issue Tracker.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Coletar um rastreamento de rede com o Fiddler (opção preferencial)

Esse método funciona para todos os aplicativos.

O Fiddler é uma ferramenta muito poderosa para coletar rastreamentos HTTP. Instale-o em [Telerik.com/Fiddler](https://www.telerik.com/fiddler), inicie-o e execute o aplicativo e reproduza o problema. O Fiddler está disponível para o Windows e há versões beta para macOS e Linux.

Se você se conectar usando HTTPS, haverá algumas etapas adicionais para garantir que o Fiddler possa descriptografar o tráfego HTTPS. Para obter mais detalhes, consulte a [documentação do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Depois de coletar o rastreamento, você pode exportar o rastreamento escolhendo **arquivo**  >  **salvar**  >  **todas as sessões** na barra de menus.

![Exportando todas as sessões do Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Coletar um rastreamento de rede com tcpdump (somente macOS e Linux)

Esse método funciona para todos os aplicativos.

Você pode coletar rastreamentos TCP brutos usando tcpdump executando o comando a seguir de um shell de comando. Talvez seja necessário ser `root` ou prefixar o comando com `sudo` se você receber um erro de permissões:

```console
tcpdump -i [interface] -w trace.pcap
```

Substitua `[interface]` pela interface de rede que você deseja capturar. Normalmente, isso é algo como `/dev/eth0` (para a sua interface Ethernet padrão) ou `/dev/lo0` (para tráfego de localhost). Para obter mais informações, consulte a `tcpdump` página do manual no sistema host.

## <a name="collect-a-network-trace-in-the-browser"></a>Coletar um rastreamento de rede no navegador

Esse método só funciona para aplicativos baseados em navegador.

A maioria dos Ferramentas para Desenvolvedores de navegador tem uma guia de "rede" que permite capturar a atividade de rede entre o navegador e o servidor. No entanto, esses rastreamentos não incluem o WebSocket e as mensagens de evento enviadas pelo servidor. Se você estiver usando esses transportes, usar uma ferramenta como Fiddler ou TcpDump (descrita abaixo) é uma abordagem melhor.

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge e Internet Explorer

(As instruções são as mesmas para o Edge e para o Internet Explorer)

1. Pressione F12 para abrir as ferramentas de desenvolvimento
2. Clique na guia rede
3. Atualizar a página (se necessário) e reproduzir o problema
4. Clique no ícone salvar na barra de ferramentas para exportar o rastreamento como um arquivo "HAR":

![O ícone salvar na guia rede de ferramentas de desenvolvimento do Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Pressione F12 para abrir as ferramentas de desenvolvimento
2. Clique na guia rede
3. Atualizar a página (se necessário) e reproduzir o problema
4. Clique com o botão direito do mouse em qualquer lugar na lista de solicitações e escolha "salvar como HAR com conteúdo":

![Opção "salvar como HAR com conteúdo" na guia rede de ferramentas de desenvolvimento do Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Pressione F12 para abrir as ferramentas de desenvolvimento
2. Clique na guia rede
3. Atualizar a página (se necessário) e reproduzir o problema
4. Clique com o botão direito do mouse em qualquer lugar na lista de solicitações e escolha "salvar tudo como HAR"

![Opção "salvar tudo como HAR" na guia de rede de ferramentas de desenvolvimento do Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Anexar arquivos de diagnóstico a problemas do GitHub

Você pode anexar arquivos de diagnóstico a problemas do GitHub renomeando-os para que eles tenham uma `.txt` extensão e, em seguida, arrastando-os e soltando-os no problema.

> [!NOTE]
> Não Cole o conteúdo dos arquivos de log ou dos rastreamentos de rede em um problema do GitHub. Esses logs e rastreamentos podem ser bem grandes, e o GitHub geralmente os trunca.

![Arrastando arquivos de log para um problema do GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a>Métricas

Métricas é uma representação de medidas de dados em intervalos de tempo. Por exemplo, solicitações por segundo. Os dados de métricas permitem a observação do estado de um aplicativo em um alto nível. As métricas do .NET gRPC são emitidas usando <xref:System.Diagnostics.Tracing.EventCounter> .

### <a name="no-locsignalr-server-metrics"></a>SignalR métricas do servidor

SignalR as métricas do servidor são relatadas na <xref:Microsoft.AspNetCore.Http.Connections> origem do evento.

| Nome                    | Descrição                 |
|-------------------------|-----------------------------|
| `connections-started`   | Total de conexões iniciadas   |
| `connections-stopped`   | Total de conexões interrompidas   |
| `connections-timed-out` | Total de conexões com tempo limite esgotado |
| `current-connections`   | Conexões atuais         |
| `connections-duration`  | Duração média da conexão |

### <a name="observe-metrics"></a>Observar métricas

[dotnet-os contadores](/dotnet/core/diagnostics/dotnet-counters) são uma ferramenta de monitoramento de desempenho para monitoramento de integridade ad hoc e investigação de desempenho de primeiro nível. Monitore um aplicativo .NET com `Microsoft.AspNetCore.Http.Connections` como o nome do provedor. Por exemplo:

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
