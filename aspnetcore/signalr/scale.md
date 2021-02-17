---
title: SignalRHospedagem e dimensionamento de produção ASP.NET Core
author: bradygaster
description: Saiba como evitar problemas de desempenho e dimensionamento em aplicativos que usam ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564014"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>SignalRHospedagem e dimensionamento de ASP.NET Core

Por [Andrew Stanton-enfermaria](https://twitter.com/anurse), [Brady GASTER](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra)

Este artigo explica as considerações de hospedagem e dimensionamento para aplicativos de alto tráfego que usam ASP.NET Core SignalR .

## <a name="sticky-sessions"></a>Sessões adesivas

SignalR requer que todas as solicitações HTTP para uma conexão específica sejam manipuladas pelo mesmo processo de servidor. Quando SignalR o está em execução em um farm de servidores (vários servidores), "sessões adesivas" devem ser usadas. "Sessões adesivas" também são chamadas de afinidade de sessão por alguns balanceadores de carga. Azure App serviço usa [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) para rotear solicitações. Habilitar a configuração "afinidade de ARR" em seu serviço de Azure App permitirá "sessões adesivas". As únicas circunstâncias nas quais as sessões adesivas não são necessárias são:

1. Ao hospedar em um único servidor, em um único processo.
1. Ao usar o serviço do Azure SignalR .
1. Quando todos os clientes são configurados para usar **apenas** WebSockets **e** a [configuração SkipNegotiation](xref:signalr/configuration#configure-additional-options) está habilitada na configuração do cliente.

Em todas as outras circunstâncias (incluindo quando o backplane Redis é usado), o ambiente de servidor deve ser configurado para sessões adesivas.

Para obter orientação sobre como configurar o serviço de Azure App para o SignalR , consulte <xref:signalr/publish-to-azure-web-app> .

## <a name="tcp-connection-resources"></a>Recursos de conexão TCP

O número de conexões TCP simultâneas às quais um servidor Web pode dar suporte é limitado. Os clientes HTTP padrão usam conexões *efêmeras* . Essas conexões podem ser fechadas quando o cliente fica ocioso e reaberto mais tarde. Por outro lado, uma SignalR conexão é *persistente*. SignalR as conexões permanecem abertas mesmo quando o cliente fica ocioso. Em um aplicativo de alto tráfego que atende a vários clientes, essas conexões persistentes podem fazer com que os servidores atinjam seu número máximo de conexões.

As conexões persistentes também consomem memória adicional para acompanhar cada conexão.

O uso intensivo de recursos relacionados à conexão SignalR pode afetar outros aplicativos Web hospedados no mesmo servidor. Quando SignalR o é aberto e mantém as últimas conexões TCP disponíveis, outros aplicativos Web no mesmo servidor também não têm mais conexões disponíveis.

Se um servidor ficar sem conexões, você verá erros de soquete aleatórios e erros de redefinição de conexão. Por exemplo:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Para impedir SignalR que o uso de recursos cause erros em outros aplicativos Web, execute SignalR em servidores diferentes dos outros aplicativos Web.

Para impedir que SignalR o uso de recursos cause erros em um SignalR aplicativo, escale horizontalmente para limitar o número de conexões que um servidor tem para lidar.

## <a name="scale-out"></a>Escalar horizontalmente

Um aplicativo que usa o SignalR precisa manter o controle de todas as suas conexões, o que cria problemas para um farm de servidores. Adicione um servidor e obtenha novas conexões que os outros servidores não conhecem. Por exemplo, SignalR em cada servidor no diagrama a seguir, não há conhecimento das conexões nos outros servidores. Quando SignalR em um dos servidores deseja enviar uma mensagem para todos os clientes, a mensagem vai apenas para os clientes conectados a esse servidor.

![Dimensionamento::: no-Loc (Signalr)::: sem um backplane](scale/_static/scale-no-backplane.png)

As opções para resolver esse problema são o [ SignalR serviço do Azure](#azure-signalr-service) e o [backplane do Redis](#redis-backplane).

## <a name="azure-signalr-service"></a>Serviço do Azure SignalR

O serviço do Azure SignalR é um proxy em vez de um backplane. Cada vez que um cliente inicia uma conexão com o servidor, o cliente é redirecionado para se conectar ao serviço. Esse processo é ilustrado no diagrama a seguir:

![Estabelecendo uma conexão com o Azure::: no-Loc (Signalr)::: Service](scale/_static/azure-signalr-service-one-connection.png)

O resultado é que o serviço gerencia todas as conexões de cliente, enquanto cada servidor precisa apenas de um pequeno número constante de conexões com o serviço, conforme mostrado no diagrama a seguir:

![Clientes conectados ao serviço, servidores conectados ao serviço](scale/_static/azure-signalr-service-multiple-connections.png)

Essa abordagem de expansão tem várias vantagens em relação à alternativa do backplane Redis:

* As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), não são necessárias, porque os clientes são redirecionados imediatamente para o serviço do Azure SignalR quando se conectam.
* Um SignalR aplicativo pode escalar horizontalmente com base no número de mensagens enviadas, enquanto o serviço do Azure é SignalR dimensionado para lidar com qualquer número de conexões. Por exemplo, pode haver milhares de clientes, mas se apenas algumas mensagens por segundo forem enviadas, o SignalR aplicativo não precisará escalar horizontalmente para vários servidores apenas para lidar com as próprias conexões.
* Um SignalR aplicativo não usará significativamente mais recursos de conexão do que um aplicativo Web sem SignalR .

Por esses motivos, recomendamos o serviço do Azure SignalR para todos os SignalR aplicativos ASP.NET Core hospedados no Azure, incluindo serviço de aplicativo, VMS e contêineres.

Para obter mais informações, consulte a [ SignalR documentação do serviço do Azure](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Backplane de Redis

[Redis](https://redis.io/) é um repositório de chave-valor na memória que dá suporte a um sistema de mensagens com um modelo de publicação/assinatura. O SignalR backplane Redis usa o recurso pub/sub para encaminhar mensagens para outros servidores. Quando um cliente faz uma conexão, as informações de conexão são passadas para o backplane. Quando um servidor deseja enviar uma mensagem a todos os clientes, ele envia ao backplane. O backplane conhece todos os clientes conectados e em quais servidores eles estão. Ele envia a mensagem a todos os clientes por meio de seus respectivos servidores. Esse processo é ilustrado no diagrama a seguir:

![Redis backplane, mensagem enviada de um servidor para todos os clientes](scale/_static/redis-backplane.png)

O backplane Redis é a abordagem de expansão recomendada para aplicativos hospedados em sua própria infraestrutura. Se houver uma latência de conexão significativa entre o data center e um data center do Azure, o serviço do Azure SignalR poderá não ser uma opção prática para aplicativos locais com requisitos de baixa latência ou alta taxa de transferência.

As vantagens do serviço do Azure SignalR anotadas anteriormente são desvantagens para o Redis backplane:

* As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), são necessárias, exceto quando **ambas** das seguintes opções são verdadeiras:
  * Todos os clientes estão configurados para usar **apenas** WebSockets.
  * A [configuração SkipNegotiation](xref:signalr/configuration#configure-additional-options) é habilitada na configuração do cliente. 
   Depois que uma conexão é iniciada em um servidor, a conexão precisa permanecer nesse servidor.
* Um SignalR aplicativo deve escalar horizontalmente com base no número de clientes, mesmo que poucas mensagens estejam sendo enviadas.
* Um SignalR aplicativo usa significativamente mais recursos de conexão do que um aplicativo Web sem o SignalR .

## <a name="iis-limitations-on-windows-client-os"></a>Limitações do IIS no sistema operacional do cliente Windows

O Windows 10 e o Windows 8. x são sistemas operacionais cliente. O IIS em sistemas operacionais cliente tem um limite de 10 conexões simultâneas. SignalRas conexões do são:

* Transitório e frequentemente restabelecida.
* **Não** Descartado imediatamente quando não é mais usado.

As condições anteriores tornam possível atingir o limite de 10 conexões em um sistema operacional cliente. Quando um sistema operacional cliente é usado para desenvolvimento, recomendamos:

* Evite o IIS.
* Use Kestrel ou IIS Express como destinos de implantação.

## <a name="linux-with-nginx"></a>Linux com o Nginx

O seguinte contém as configurações mínimas necessárias para habilitar WebSockets, ServerSentEvents e LongPolling para SignalR :

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

Quando vários servidores de back-end são usados, as sessões adesivas devem ser adicionadas para impedir que SignalR as conexões alternem os servidores ao se conectarem. Há várias maneiras de adicionar sessões adesivas no nginx. Duas abordagens são mostradas abaixo, dependendo do que você tiver disponível.

O seguinte é adicionado além da configuração anterior. Nos exemplos a seguir, `backend` é o nome do grupo de servidores.

Com o [Nginx Open Source](https://nginx.org/en/), use `ip_hash` para rotear conexões para um servidor com base no endereço IP do cliente:

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

Com o [Nginx Plus](https://www.nginx.com/products/nginx), use `sticky` para adicionar um cookie a às solicitações e fixar as solicitações do usuário em um servidor:

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

Por fim, altere `proxy_pass http://localhost:5000` na `server` seção para `proxy_pass http://backend` .

Para obter mais informações sobre WebSockets em Nginx, consulte [Nginx como um proxy WebSocket](https://www.nginx.com/blog/websocket-nginx).

Para obter mais informações sobre balanceamento de carga e sessões adesivas, consulte [balanceamento de carga Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).

Para obter mais informações sobre ASP.NET Core com Nginx, consulte o seguinte artigo:
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a>SignalRProvedores de backplane de terceiros

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Orleans](https://github.com/OrleansContrib/SignalR.Orleans)
* [Rebus](https://github.com/rebus-org/Rebus.SignalR)

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Documentação do serviço do Azure SignalR](/azure/azure-signalr/signalr-overview)
* [Configurar um backplane Redis](xref:signalr/redis-backplane)
