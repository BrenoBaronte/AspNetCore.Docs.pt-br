---
title: Implementações de servidor Web em ASP.NET Core
author: rick-anderson
description: Descubra os servidores Web Kestrel e HTTP.sys para ASP.NET Core. Saiba como escolher um servidor e quando usar um servidor proxy reverso.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: a49388c73f2ec0ea03b35dbba1575ce70a4e1512
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238333"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implementações de servidor Web em ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) e [Chris Ross](https://github.com/Tratcher)

Um aplicativo ASP.NET Core é executado com uma implementação do servidor HTTP em processo. A implementação do servidor escuta solicitações HTTP e apresenta-as para o aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um <xref:Microsoft.AspNetCore.Http.HttpContext>.

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

O ASP.NET Core vem com os seguintes itens:

* O [servidor Kestrel](xref:fundamentals/servers/kestrel) é a implementação padrão do servidor HTTP multiplataforma. O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados do HTTP.sys. Para obter mais informações, consulte [Kestrel vs. HTTP.sys](#korh) na próxima seção.
* O servidor HTTP do IIS é um [servidor em processo](#hosting-models) do IIS.
* O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor HTTP somente do Windows com base no [driver do kernel HTTP.sys e na API do servidor HTTP](/windows/desktop/Http/http-api-start-page).

Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado:

* No mesmo processo que o processo de trabalho do IIS (o [modelo de hospedagem em processo](#hosting-models)) com o servidor http IIS. *Em processo* é a configuração recomendada.
* Em um processo separado do processo de trabalho do IIS (o [modelo de hospedagem fora do processo](#hosting-models)) com o [servidor Kestrel](#kestrel).

O [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) é um módulo nativo do IIS que manipula as solicitações nativas do IIS entre o IIS e o servidor HTTP do IIS em processo ou o Kestrel. Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a>Kestrel versus HTTP.sys

O Kestrel tem as seguintes vantagens em relação ao HTTP.sys:

  * Melhor utilização de memória e desempenho.
  * Plataforma cruzada
  * Agilidade, ela é desenvolvida e corrigida de forma independente do sistema operacional.
  * Configuração de TLS e porta programática
  * Extensibilidade que permite protocolos como [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) e transportes alternativos.

Http.Sys funciona como um componente de modo kernel compartilhado com os seguintes recursos que o Kestrel não tem:

  * Compartilhamento de porta
  * Autenticação do Windows no modo kernel. O [Kestrel dá suporte apenas à autenticação de modo de usuário](xref:security/authentication/windowsauth#kestrel).
  * Proxy rápido por meio de transferências de fila
  * Transmissão direta de arquivo
  * Cache de resposta

## <a name="hosting-models"></a>Modelos de hospedagem

Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS. A hospedagem em processo oferece desempenho melhor em hospedagem fora do processo porque as solicitações não são transmitidas por proxy pelo adaptador de loopback, um adaptador de rede que retorna o tráfego de rede de saída para o mesmo computador. O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Usando a hospedagem fora do processo, os aplicativos do ASP.NET Core são executados em um processo separado do processo de trabalho do IIS e o módulo cuida do gerenciamento de processos. O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha. Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Para obter mais informações e orientação sobre a configuração, consulte os seguintes tópicos:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.

# <a name="linux"></a>[Linux](#tab/linux)

O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

 O [servidor Kestrel](xref:fundamentals/servers/kestrel) é a implementação padrão do servidor HTTP multiplataforma. O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados do HTTP.sys. Para obter mais informações, consulte [Kestrel vs. HTTP.sys](#korh) neste documento.

Use o Kestrel:

* Sozinho, como um servidor de borda que processa solicitações diretamente de uma rede, incluindo a Internet.

  ![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

* Com um *servidor proxy reverso* como [IIS (Serviços de Informações da Internet)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/). Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.

  ![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

&mdash;Há suporte para hospedar a configuração com ou sem um servidor proxy reverso &mdash; .

Para obter diretrizes de configuração do Kestrel e informações sobre quando usar o Kestrel em uma configuração de proxy reverso, confira <xref:fundamentals/servers/kestrel>.

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

O ASP.NET Core vem com os seguintes itens:

* O [servidor Kestrel](xref:fundamentals/servers/kestrel) é o servidor HTTP padrão multiplataforma.
* O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor HTTP somente do Windows com base no [driver do kernel HTTP.sys e na API do servidor HTTP](/windows/desktop/Http/http-api-start-page).

Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado em um processo separado do processo de trabalho do IIS (*fora do processo*) com o [servidor Kestrel](#kestrel).

Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos. O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha. Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:

![Módulo do ASP.NET Core](_static/ancm-outofprocess.png)

As solicitações chegam da Web para o driver do HTTP.sys no modo kernel. O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS). O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.

O módulo Especifica a porta por meio de uma variável de ambiente na inicialização, e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor a ser escutado `http://localhost:{port}` . Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas. O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.

Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel. A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.

Para obter as diretrizes de configuração do IIS e do módulo do ASP.NET Core, confira os tópicos a seguir:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.

# <a name="linux"></a>[Linux](#tab/linux)

O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx com Kestrel

Para obter informações sobre como usar Nginx no Linux como um servidor proxy reverso para Kestrel, confira <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Apache com Kestrel

Para obter informações sobre como usar Apache no Linux como um servidor proxy reverso para Kestrel, confira <xref:host-and-deploy/linux-apache>.

## <a name="httpsys"></a>HTTP.sys

Se os aplicativos ASP.NET Core forem executados no Windows, o HTTP.sys será uma alternativa ao Kestrel. O Kestrel é recomendado em HTTP.sys, a menos que o aplicativo exija recursos não disponíveis no Kestrel. Para obter mais informações, consulte <xref:fundamentals/servers/httpsys>.

![O HTTP.sys se comunica diretamente com a Internet](httpsys/_static/httpsys-to-internet.png)

O HTTP.sys também pode ser usado para aplicativos que são expostos somente a uma rede interna.

![O HTTP.sys se comunica diretamente com a rede interna](httpsys/_static/httpsys-to-internal.png)

Para obter as diretrizes de configuração do HTTP.sys, confira <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>Infraestrutura de servidor do ASP.NET Core

O <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponível no método `Startup.Configure` expõe a propriedade <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> do tipo <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>. O Kestrel e o HTTP.sys expõem apenas um único recurso cada, o <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, mas diferentes implementações de servidor podem expor funcionalidades adicionais.

`IServerAddressesFeature` pode ser usado para descobrir a qual porta a implementação do servidor se acoplou durante o runtime.

## <a name="custom-servers"></a>Servidores personalizados

Se os servidores internos não atenderem aos requisitos do aplicativo, um servidor personalizado poderá ser criado. O [Guia de OWIN (Open Web Interface para .NET)](xref:fundamentals/owin) demonstra como gravar uma implementação [com base em ](https://github.com/Bobris/Nowin)Nowin<xref:Microsoft.AspNetCore.Hosting.Server.IServer>. Somente as interfaces de recurso que o aplicativo usa exigem implementação, embora no mínimo <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> e <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> devam ser compatíveis.

## <a name="server-startup"></a>Inicialização do servidor

O servidor é iniciado quando o IDE (Ambiente de Desenvolvimento Integrado) ou o editor inicia o aplicativo:

* [Visual Studio](https://visualstudio.microsoft.com): os perfis de inicialização podem ser usados para iniciar o aplicativo e o servidor com o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core módulo](xref:host-and-deploy/aspnet-core-module) ou o console do.
* [Visual Studio Code](https://code.visualstudio.com/): o aplicativo e o servidor são iniciados pelo [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), que ativa o depurador CoreCLR.
* [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/): o aplicativo e o servidor são iniciados pelo [depurador de Soft-Mode mono](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Ao iniciar o aplicativo usando um prompt de comando na pasta do projeto, o [dotnet run](/dotnet/core/tools/dotnet-run) inicia o aplicativo e o servidor (apenas Kestrel e HTTP.sys). A configuração é especificada pela opção `-c|--configuration`, que é definida como `Debug` (padrão) ou `Release`.

Um *launchSettings.jsno* arquivo fornece configuração ao iniciar um aplicativo com `dotnet run` o ou com um depurador interno de ferramentas, como o Visual Studio. Se os perfis de inicialização estiverem presentes em um *launchSettings.jsno* arquivo, use a `--launch-profile {PROFILE NAME}` opção com o `dotnet run` comando ou selecione o perfil no Visual Studio. Para obter mais informações, confira [dotnet run](/dotnet/core/tools/dotnet-run) e [pacote de distribuição do .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Suporte do HTTP/2

O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação:

::: moniker range=">= aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel/http2)
  * Sistema operacional
    * Windows Server 2016/Windows 10 ou posterior&dagger;
    * Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)
    * O HTTP/2 será compatível com macOS em uma versão futura.
  * Estrutura de destino: .NET Core 2.2 ou posterior
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 ou posterior
  * Estrutura de destino: não aplicável a implantações de HTTP.sys.
* [IIS (em processo)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Estrutura de destino: .NET Core 2.2 ou posterior
* [IIS (fora do processo)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.
  * Estrutura de destino: não aplicável a implantações IIS fora de processo.

&dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1. O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada. Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Sistema operacional
    * Windows Server 2016/Windows 10 ou posterior&dagger;
    * Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)
    * O HTTP/2 será compatível com macOS em uma versão futura.
  * Estrutura de destino: .NET Core 2.2 ou posterior
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 ou posterior
  * Estrutura de destino: não aplicável a implantações de HTTP.sys.
* [IIS (em processo)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Estrutura de destino: .NET Core 2.2 ou posterior
* [IIS (fora do processo)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.
  * Estrutura de destino: não aplicável a implantações IIS fora de processo.

&dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1. O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada. Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 ou posterior
  * Estrutura de destino: não aplicável a implantações de HTTP.sys.
* [IIS (fora do processo)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.
  * Estrutura de destino: não aplicável a implantações IIS fora de processo.

::: moniker-end

Uma conexão HTTP/2 precisa usar [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) e TLS 1.2 ou posterior. Para obter mais informações, consulte os tópicos referentes aos seus cenários de implantação do servidor.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
