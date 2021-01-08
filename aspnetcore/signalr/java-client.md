---
title: ASP.NET Core SignalR cliente Java
author: mikaelm12
description: Saiba como usar o SignalR cliente Java ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: 92941d21820de90eb2ae8fb76c21c588ed9f1ffb
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024750"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a>ASP.NET Core SignalR cliente Java

Por [Mikael Mengistu](https://twitter.com/MikaelM_12)

O cliente Java permite conectar-se a um SignalR servidor ASP.NET Core do código Java, incluindo aplicativos Android. Assim como o cliente [JavaScript](xref:signalr/javascript-client) e o [cliente .net](xref:signalr/dotnet-client), o cliente Java permite que você receba e envie mensagens para um Hub em tempo real. O cliente Java está disponível no ASP.NET Core 2,2 e posterior.

O aplicativo de console Java de exemplo referenciado neste artigo usa o SignalR cliente Java.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-java-client-package"></a>Instalar o SignalR pacote do cliente Java

O arquivo *signalr-1.0.0* jar permite que os clientes se conectem a SignalR hubs. Para localizar o número de versão mais recente do arquivo JAR, consulte os [resultados da pesquisa do Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Se estiver usando gradle, adicione a seguinte linha à `dependencies` seção do arquivo *Build. gradle* :

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Se estiver usando o Maven, adicione as seguintes linhas dentro do `<dependencies>` elemento do seu arquivo de *pom.xml* :

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Conectar-se a um hub

Para estabelecer um `HubConnection` , o `HubConnectionBuilder` deve ser usado. A URL do Hub e o nível de log podem ser configurados durante a criação de uma conexão. Configure as opções necessárias chamando qualquer um dos `HubConnectionBuilder` métodos antes `build` . Inicie a conexão com `start` .

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Métodos do hub de chamadas do cliente

Uma chamada para `send` invoca um método de Hub. Passe o nome do método de Hub e quaisquer argumentos definidos no método de Hub para `send` .

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Somente há suporte para métodos de Hub de chamada de um cliente ao usar o serviço do Azure SignalR no modo *padrão* . Para obter mais informações, consulte perguntas frequentes [(repositório GitHub do Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="call-client-methods-from-hub"></a>Chamar métodos de cliente do Hub

Use `hubConnection.on` para definir métodos no cliente que o Hub pode chamar. Defina os métodos após a compilação, mas antes de iniciar a conexão.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Adicionar registro em log

O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log. É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica. O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado com segurança.

## <a name="android-development-notes"></a>Notas de desenvolvimento do Android

Com relação à compatibilidade SDK do Android para os SignalR recursos do cliente, considere os seguintes itens ao especificar sua versão de SDK do Android de destino:

* O SignalR cliente Java será executado no nível 16 da API do Android e posterior.
* A conexão por meio do serviço do Azure SignalR exigirá o nível 20 da API do Android e posterior, pois o [ SignalR serviço do Azure](/azure/azure-signalr/signalr-overview) requer o TLS 1,2 e não oferece suporte a conjuntos de codificação baseados em SHA-1. O Android [adicionou suporte para pacotes de criptografia SHA-256 (e superior)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) no nível de API 20.

## <a name="configure-bearer-token-authentication"></a>Configurar autenticação de token de portador

No SignalR cliente Java, você pode configurar um token de portador a ser usado para autenticação, fornecendo uma "fábrica de token de acesso" ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html). Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a>Passando informações de classe em Java

Ao chamar os `on` `invoke` métodos, ou `stream` de `HubConnection` no cliente Java, os usuários devem passar um `Type` objeto em vez de um `Class<?>` objeto para descrever qualquer genérico `Object` passado para o método. Um `Type` pode ser adquirido usando a `TypeReference` classe fornecida. Por exemplo, usando uma classe genérica personalizada chamada `Foo<T>` , o código a seguir obtém o `Type` :

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

Para não genéricos, como primitivos ou outros tipos não parametrizados como `String` , você pode simplesmente usar o interno `.class` .

Ao chamar um desses métodos com um ou mais tipos de objeto, use a sintaxe genérica ao invocar o método. Por exemplo, ao registrar um `on` manipulador para um método chamado `func` , que usa como argumentos uma cadeia de caracteres e um `Foo<String>` objeto, use o seguinte código para definir uma ação para imprimir os argumentos:

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

Essa convenção é necessária porque não podemos recuperar informações completas sobre tipos complexos com o `Object.getClass` método devido à eliminação de tipo em Java. Por exemplo, chamar `getClass` em um `ArrayList<String>` não retornaria `Class<ArrayList<String>>` , mas em vez disso `Class<ArrayList>` , que não dá ao desserializador informações suficientes para desserializar corretamente uma mensagem de entrada. O mesmo é verdadeiro para objetos personalizados.

::: moniker-end

## <a name="known-limitations"></a>Limitações conhecidas

::: moniker range=">= aspnetcore-5.0"

* O fallback de transporte e o transporte de eventos do servidor enviados não têm suporte.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* O fallback de transporte e o transporte de eventos do servidor enviados não têm suporte.
* Somente o protocolo JSON tem suporte.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Somente o protocolo JSON tem suporte.
* Somente o transporte WebSockets tem suporte.
* Não há suporte para streaming ainda.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Referência da API Java](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Documentação sem servidor do serviço do Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)
