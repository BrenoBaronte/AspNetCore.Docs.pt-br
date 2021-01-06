---
title: Hospedar e implantar ASP.NET Core Blazor Server
author: guardrex
description: Saiba como hospedar e implantar um Blazor Server aplicativo usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94595435"
---
# <a name="host-and-deploy-no-locblazor-server"></a>Hospedar e implantar Blazor Server

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuração do host

os [ Blazor Server aplicativos](xref:blazor/hosting-models#blazor-server) podem aceitar [valores de configuração de host genéricos](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Implantação

Usando o [ Blazor Server modelo de hospedagem](xref:blazor/hosting-models#blazor-server), o Blazor é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma [SignalR](xref:signalr/introduction) conexão.

É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core. O Visual Studio inclui o modelo de projeto de **Blazor Server aplicativo** ( `blazorserverside` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando).

## <a name="scalability"></a>Escalabilidade

Planeje uma implantação para fazer o melhor uso da infraestrutura disponível para um Blazor Server aplicativo. Consulte os seguintes recursos para abordar a Blazor Server escalabilidade do aplicativo:

* [Conceitos básicos de Blazor Server aplicativos](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>Servidor de implantação

Ao considerar a escalabilidade de um único servidor (escalar verticalmente), a memória disponível para um aplicativo provavelmente será o primeiro recurso que o aplicativo esgotará conforme as demandas do usuário aumentarem. A memória disponível no servidor afeta o:

* Número de circuitos ativos aos quais um servidor pode dar suporte.
* Latência da interface do usuário no cliente.

Para obter orientação sobre a criação de Blazor aplicativos de servidor seguros e escalonáveis, consulte <xref:blazor/security/server/threat-mitigation> .

Cada circuito usa aproximadamente 250 KB de memória para um aplicativo estilo mínimo de *Olá, mundo*. O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção de estado associados a cada componente. Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infraestrutura, mas a linha de base a seguir pode ser um ponto de partida para planejar seu destino de implantação: se você espera que seu aplicativo ofereça suporte a 5.000 usuários simultâneos, considere o orçamento de pelo menos 1,3 GB de memória do servidor para o aplicativo (ou ~ 273 KB por usuário).

### <a name="no-locsignalr-configuration"></a>SignalR configuração

Blazor Server os aplicativos usam ASP.NET Core SignalR para se comunicar com o navegador. as [ SignalR condições de hospedagem e dimensionamento do](xref:signalr/publish-to-azure-web-app) se aplicam aos Blazor Server aplicativos.

Blazor funciona melhor ao usar o WebSocket como SignalR transporte devido à latência, confiabilidade e [segurança](xref:signalr/security)menores. A sondagem longa é usada pelo SignalR quando o WebSockets não está disponível ou quando o aplicativo é explicitamente configurado para usar sondagem longa. Ao implantar no serviço Azure App, configure o aplicativo para usar Websockets nas configurações de portal do Azure para o serviço. Para obter detalhes sobre como configurar o aplicativo para Azure App serviço, consulte as [ SignalR diretrizes de publicação](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-no-locsignalr-service"></a>Serviço do Azure SignalR

É recomendável usar [o SignalR serviço do Azure](xref:signalr/scale#azure-signalr-service) para Blazor Server aplicativos. O serviço permite escalar verticalmente um Blazor Server aplicativo para um grande número de conexões simultâneas SignalR . Além disso, o SignalR alcance global do serviço e os data centers de alto desempenho auxiliam significativamente na redução da latência devido à geografia.

> [!IMPORTANT]
> Quando os [WebSockets](https://wikipedia.org/wiki/WebSocket) estão desabilitados, Azure app serviço simula uma conexão em tempo real usando a sondagem longa http. A sondagem longa HTTP é visivelmente mais lenta do que a execução com WebSockets habilitados, o que não usa a sondagem para simular uma conexão cliente-servidor.
>
> É recomendável usar Websockets para Blazor Server aplicativos implantados no serviço Azure app. O [ SignalR serviço do Azure](xref:signalr/scale#azure-signalr-service) usa o WebSocket por padrão. Se o aplicativo não usar o serviço do Azure SignalR , consulte <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .
>
> Para obter mais informações, consulte:
>
> * [O que é o serviço do Azure SignalR ?](/azure/azure-signalr/signalr-overview)
> * [Guia de desempenho do SignalR serviço do Azure](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a>Configuração

Para configurar um aplicativo para o serviço do Azure SignalR , o aplicativo deve dar suporte a *sessões adesivas*, em que os clientes são [redirecionados de volta para o mesmo servidor durante o pré-processamento](xref:blazor/hosting-models#connection-to-the-server). A `ServerStickyMode` opção ou o valor de configuração é definido como `Required` . Normalmente, um aplicativo cria a configuração usando **_uma_** das seguintes abordagens:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuração (use **_uma_** das seguintes abordagens):
  
     * Em `appsettings.json`:

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * As configurações do aplicativo de **configuração** do serviço de aplicativo  >   na portal do Azure (**nome**: `Azure__SignalR__StickyServerMode` , **valor**: `Required` ). Essa abordagem será adotada para o aplicativo automaticamente se você [provisionar o SignalR serviço do Azure](#provision-the-azure-signalr-service).

### <a name="provision-the-azure-no-locsignalr-service"></a>Provisionar o serviço do Azure SignalR

Para provisionar o serviço do Azure SignalR para um aplicativo no Visual Studio:

1. Crie um perfil de publicação de aplicativos do Azure no Visual Studio para o Blazor Server aplicativo.
1. Adicione a dependência de **SignalR serviço do Azure** ao perfil. Se a assinatura do Azure não tiver uma instância de serviço do Azure já existente SignalR para atribuir ao aplicativo, selecione **criar uma nova SignalR instância de serviço do Azure** para provisionar uma nova instância de serviço.
1. Publicar o aplicativo no Azure.

O provisionamento do serviço do Azure SignalR no Visual Studio habilita automaticamente as [ *sessões adesivas*](#configuration) e adiciona a SignalR cadeia de conexão à configuração do serviço de aplicativo.

#### <a name="iis"></a>IIS

Ao usar o IIS, habilite:

* [WebSockets no IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sessões adesivas com Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Crie uma definição de entrada com as seguintes [anotações de kubernetes para sessões adesivas](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>Linux com o Nginx

Para SignalR que o WebSockets funcione corretamente, confirme se os `Upgrade` cabeçalhos e o proxy `Connection` estão definidos com os seguintes valores e se estão `$connection_upgrade` mapeados para:

* O valor do cabeçalho de atualização por padrão.
* `close` Quando o cabeçalho de atualização está ausente ou vazio.

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

Para obter mais informações, consulte os seguintes artigos:

* [NGINX como um proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxy WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>Linux com o Apache

Para hospedar um Blazor aplicativo por trás do Apache no Linux, configure `ProxyPass` para tráfego HTTP e WebSockets.

No exemplo a seguir:

* O servidor Kestrel está em execução no computador host.
* O aplicativo escuta o tráfego na porta 5000.

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

Habilite os seguintes módulos:

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

Verifique se há erros de WebSocket no console do navegador. Erros de exemplo:

* O Firefox não pode estabelecer uma conexão com o servidor em ws://the-domain-name.tld/_blazor?id=XXX.
* Erro: falha ao iniciar o transporte ' WebSockets ': erro: erro no transporte.
* Erro: falha ao iniciar o transporte ' LongPolling ': TypeError: this. Transport está indefinido
* Erro: não é possível se conectar ao servidor com nenhum dos transportes disponíveis. Falha no WebSockets
* Erro: não é possível enviar dados se a conexão não estiver no estado "conectado".

Para obter mais informações, consulte a [documentação do Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).

### <a name="measure-network-latency"></a>Medir latência de rede

A [interoperabilidade js](xref:blazor/call-javascript-from-dotnet) pode ser usada para medir a latência de rede, como demonstra o exemplo a seguir:

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

Para uma experiência de interface do usuário razoável, recomendamos uma latência de interface do usuário sustentada de 250 MS ou menos.
