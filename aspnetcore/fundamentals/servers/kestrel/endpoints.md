---
title: Configurar pontos de extremidade para o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba como configurar pontos de extremidade com o Kestrel, o servidor Web de plataforma cruzada para ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: f9d82409f4b31a5564c7cdfa48beb303d784e213
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057142"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a>Configurar pontos de extremidade para o servidor Web ASP.NET Core Kestrel

Por padrão, o ASP.NET Core associa a:

* `http://localhost:5000`
* `https://localhost:5001` (quando um certificado de desenvolvimento local está presente)

Especificar URLs usando:

* A variável de ambiente `ASPNETCORE_URLS`.
* O argumento de linha de comando `--urls`.
* A chave de configuração do host `urls`.
* O método de extensão `UseUrls`.

O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão). Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).

Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).

Um certificado de desenvolvimento é criado:

* Quando o [SDK do .net](/dotnet/core/sdk) é instalado.
* A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.

Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.

Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).

Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.

`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).

`KestrelServerOptions` configuração

## <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (ação \<ListenOptions> )

Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado. Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> de chamar não terão os padrões aplicados.

## <a name="configureiconfiguration"></a>Configure(IConfiguration)

Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada. A configuração precisa estar no escopo da seção de configuração do Kestrel.

`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )

Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS. Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> de chamar não terão os padrões aplicados.

## <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Configure o Kestrel para usar HTTPS.

Extensões `ListenOptions.UseHttps`:

* `UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão. Gera uma exceção quando não há nenhum certificado padrão configurado.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

Parâmetros de `ListenOptions.UseHttps`:

* `filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.
* `password` é a senha necessária para acessar os dados do certificado X.509 .
* `configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`. Retorna o `ListenOptions`.
* `storeName` é o repositório de certificados do qual o certificado deve ser carregado.
* `subject` é o nome da entidade do certificado.
* `allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.
* `location` é o local do repositório do qual o certificado deve ser carregado.
* `serverCertificate` é o certificado X.509.

Em produção, HTTPS precisa ser configurado explicitamente. No mínimo, um certificado padrão precisa ser fornecido.

Configurações com suporte descritas a seguir:

* Nenhuma configuração
* Substituir o certificado padrão da configuração
* Alterar os padrões no código

### <a name="no-configuration"></a>Nenhuma configuração

O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a>Substituir o certificado padrão da configuração

Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel. Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.

No exemplo a seguir *appsettings.json* :

* Defina `AllowInvalid` como `true` para permitir o uso de certificados inválidos (por exemplo, certificados autoassinados).
* Qualquer ponto de extremidade HTTPS que não especifique um certificado ( `HttpsDefaultCert` no exemplo a seguir) volta para o certificado definido em `Certificates:Default` ou o certificado de desenvolvimento.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Observações do esquema:

* Os nomes dos pontos de extremidade não diferenciam [maiúsculas de minúsculas](xref:fundamentals/configuration/index#configuration-keys-and-values). Por exemplo: `HTTPS` and `Https` são equivalentes.
* O parâmetro `Url` é necessário para cada ponto de extremidade. O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.
* Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles. Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.
* A seção `Certificate` é opcional. Se a `Certificate` seção não for especificada, os padrões definidos em `Certificates:Default` serão usados. Se nenhum padrão estiver disponível, o certificado de desenvolvimento será usado. Se não houver padrões e o certificado de desenvolvimento não estiver presente, o servidor gerará uma exceção e não será iniciado.
* A `Certificate` seção dá suporte a várias [fontes de certificado](#certificate-sources).
* Qualquer número de pontos de extremidade pode ser definido na [configuração](xref:fundamentals/configuration/index) contanto que eles não causem conflitos de porta.

#### <a name="certificate-sources"></a>Fontes de certificado

Os nós de certificado podem ser configurados para carregar certificados de várias fontes:

* `Path` e `Password` carregar arquivos *. pfx* .
* `Path``KeyPath`e `Password` para carregar arquivos. *PEM* / *. CRT* e *. Key* .
* `Subject` e `Store` para carregar do repositório de certificados.

Por exemplo, o `Certificates:Default` certificado pode ser especificado como:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a>ConfigurationLoader

`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .

* A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.
* Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção. Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores. O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.
* O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local. Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.

### <a name="change-the-defaults-in-code"></a>Alterar os padrões no código

`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior. `ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a>Configurar pontos de extremidade usando o Indicação de Nome de Servidor

A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta. Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto. O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.

O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`. O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado. O código de retorno de chamada a seguir pode ser usado na `ConfigureWebHostDefaults` chamada de método do arquivo *Program.cs* de um projeto:

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

O suporte para SNI requer:

* Em execução na estrutura de destino `netcoreapp2.1` ou posterior. No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` . O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.
* Todos os sites executados na mesma instância do Kestrel. Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.

## <a name="ssltls-protocols"></a>Protocolos SSL/TLS

Os protocolos SSL são protocolos usados para criptografar e descriptografar o tráfego entre dois pares, tradicionalmente um cliente e um servidor.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

O valor padrão, `SslProtocols.None` , faz com que o Kestrel use os padrões do sistema operacional para escolher o melhor protocolo. A menos que você tenha um motivo específico para selecionar um protocolo, use o padrão.
## <a name="connection-logging"></a>Log de conexão

Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão. O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies. Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado. Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado. Este é o middleware de [conexão](#connection-middleware)interno.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a>Associar a um soquete TCP

O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a>Associar a um soquete do UNIX

Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* No arquivo de configuração Nginx, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` . `{KESTREL SOCKET}` é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (por exemplo, `kestrel-test.sock` no exemplo anterior).
* Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).

## <a name="port-0"></a>Porta 0

Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível. O exemplo a seguir mostra como determinar qual porta Kestrel ligada no tempo de execução:

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a>Limitações

Configure pontos de extremidade com as seguintes abordagens:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* O argumento de linha de comando `--urls`
* A chave de configuração do host `urls`
* A variável de ambiente `ASPNETCORE_URLS`

Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel. No entanto, esteja ciente das seguintes limitações:

* O HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando `KestrelServerOptions` configuração ou um arquivo de configuração, como mostrado anteriormente neste artigo).
* Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.

## <a name="iis-endpoint-configuration"></a>Configuração de ponto de extremidade do IIS

Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`. Para obter mais informações, consulte [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).

## <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor. Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.

| Valor de enumeração `HttpProtocols` | Protocolo de conexão permitido |
| -------------------------- | ----------------------------- |
| `Http1`                    | HTTP/1.1 apenas. Pode ser usado com ou sem TLS. |
| `Http2`                    | HTTP/2 apenas. Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 e HTTP/2. O HTTP/2 requer que o cliente selecione HTTP/2 no handshake de [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS; caso contrário, a conexão será padronizada como HTTP/1.1. |

O `ListenOptions.Protocols` valor padrão para qualquer ponto de extremidade é `HttpProtocols.Http1AndHttp2` .

Restrições TLS para HTTP/2:

* Versão TLS 1.2 ou posterior
* Renegociação desabilitada
* Compactação desabilitada
* Tamanhos mínimos de troca de chaves efêmera:
  * Diffie-Hellman de curva elíptica (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : mínimo de 224 bits
  * Diffie-Hellman de campo finito (DHE) &lbrack; `TLS12` &rbrack; : mínimo de 2048 bits
* Conjunto de codificação não proibido. 

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 tem &lbrack; `FIPS186` &rbrack; suporte por padrão.

O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000. As conexões são protegidas pela TLS com um certificado fornecido:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

No Linux, <xref:System.Net.Security.CipherSuitesPolicy> o pode ser usado para filtrar os Handshakes de TLS em uma base por conexão:

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a>Middleware de conexão

O middleware de conexão personalizada pode filtrar os Handshakes TLS em uma base por conexão para codificações específicas, se necessário.

O exemplo a seguir gera <xref:System.NotSupportedException> um algoritmo de codificação para o qual o aplicativo não dá suporte. Como alternativa, defina e compare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) com uma lista de pacotes de codificação aceitáveis.

Nenhuma criptografia é usada com um algoritmo de codificação [CipherAlgorithmType. NULL](xref:System.Security.Authentication.CipherAlgorithmType) .

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

A filtragem de conexão também pode ser configurada por meio de um <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a>Definir o protocolo HTTP com base na configuração

`CreateDefaultBuilder` chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.

O exemplo a seguir *appsettings.json* estabelece http/1.1 como o protocolo de conexão padrão para todos os pontos de extremidade:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

O exemplo a seguir *appsettings.json* estabelece o protocolo de conexão HTTP/1.1 para um ponto de extremidade específico:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

Os protocolos especificados no código substituem os valores definidos pela configuração.

## <a name="url-prefixes"></a>Prefixos de URL

Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.

Somente os prefixos de URL HTTP são válidos. O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.

* Endereço IPv4 com o número da porta

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` é um caso especial que associa a todos os endereços IPv4.

* Endereço IPv6 com número da porta

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.

* Nome do host com o número da porta

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Os nomes de host `*` e `+` não são especiais. Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4. Para associar nomes de host diferentes a diferentes aplicativos de ASP.NET Core na mesma porta, use [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso. Os exemplos de servidor proxy reverso incluem IIS, Nginx ou Apache.

  > [!WARNING]
  > A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](xref:fundamentals/servers/kestrel/host-filtering).

* Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6. Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado. Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.
