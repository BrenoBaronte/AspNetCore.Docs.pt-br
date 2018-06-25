---
title: Módulos do IIS com o ASP.NET Core
author: guardrex
description: Descubra módulos ativos e inativos do IIS para aplicativos do ASP.NET Core e como gerenciar os módulos do IIS.
ms.author: riande
ms.custom: mvc
ms.date: 04/04/2018
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 1ff0fdcaae066b493eeebf6a061e383f88c81052
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36272731"
---
# <a name="iis-modules-with-aspnet-core"></a>Módulos do IIS com o ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Aplicativos do ASP.NET Core são hospedados pelo IIS em uma configuração de proxy reverso. Alguns dos módulos nativos do IIS e todos os módulos gerenciados do IIS não estão disponíveis para processar solicitações para aplicativos do ASP.NET Core. Em muitos casos, o ASP.NET Core oferece uma alternativa para os recursos de módulos gerenciados e nativos do IIS.

## <a name="native-modules"></a>Módulos nativos

A tabela indica módulos nativos do IIS que estão funcionando em solicitações de proxy reverso para aplicativos do ASP.NET Core.

| Módulo | Funcional com os aplicativos do ASP.NET Core | Opção do ASP.NET Core |
| ------ | :-------------------------------: | ------------------- |
| **Autenticação anônima**<br>`AnonymousAuthenticationModule` | Sim | |
| **Autenticação básica**<br>`BasicAuthenticationModule` | Sim | |
| **Autenticação de mapeamento de certificação de cliente**<br>`CertificateMappingAuthenticationModule` | Sim | |
| **CGI**<br>`CgiModule` | Não | |
| **Validação da configuração**<br>`ConfigurationValidationModule` | Sim | |
| **Erros HTTP**<br>`CustomErrorModule` | Não | [Middleware de páginas de código de status](xref:fundamentals/error-handling#configuring-status-code-pages) |
| **Registro em log personalizado**<br>`CustomLoggingModule` | Sim | |
| **Documento padrão**<br>`DefaultDocumentModule` | Não | [Middleware de arquivos padrão](xref:fundamentals/static-files#serve-a-default-document) |
| **Autenticação Digest**<br>`DigestAuthenticationModule` | Sim | |
| **Pesquisa no Diretório**<br>`DirectoryListingModule` | Não | [Middleware de navegação no diretório](xref:fundamentals/static-files#enable-directory-browsing) |
| **Compactação dinâmica**<br>`DynamicCompressionModule` | Sim | [Middleware de compactação de resposta](xref:performance/response-compression) |
| **Rastreamento**<br>`FailedRequestsTracingModule` | Sim | [Registro em log do ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Cache de arquivo**<br>`FileCacheModule` | Não | [Middleware de Cache de Resposta](xref:performance/caching/middleware) |
| **Cache HTTP**<br>`HttpCacheModule` | Não | [Middleware de Cache de Resposta](xref:performance/caching/middleware) |
| **Log HTTP**<br>`HttpLoggingModule` | Sim | [Registro em log do ASP.NET Core](xref:fundamentals/logging/index)<br>Implementações: [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging), [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging), [NLog](https://github.com/NLog/NLog.Extensions.Logging), [Serilog](https://github.com/serilog/serilog-extensions-logging)
| **Redirecionamento de HTTP**<br>`HttpRedirectionModule` | Sim | [Middleware de regravação de URL](xref:fundamentals/url-rewriting) |
| **Autenticação de mapeamento de certificado do cliente IIS**<br>`IISCertificateMappingAuthenticationModule` | Sim | |
| **Restrições de IP e domínio**<br>`IpRestrictionModule` | Sim | |
| **Filtros ISAPI**<br>`IsapiFilterModule` | Sim | [Middleware](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule` | Sim | [Middleware](xref:fundamentals/middleware/index) |
| **Suporte de protocolo**<br>`ProtocolSupportModule` | Sim | |
| **Filtragem de Solicitações**<br>`RequestFilteringModule` | Sim | [Middleware de regravação de URL `IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Monitor de Solicitações**<br>`RequestMonitorModule` | Sim | |
| **Regravação de URL**<br>`RewriteModule` | Sim&#8224; | [Middleware de regravação de URL](xref:fundamentals/url-rewriting) |
| **Inclusões do lado do servidor**<br>`ServerSideIncludeModule` | Não | |
| **Compactação estática**<br>`StaticCompressionModule` | Não | [Middleware de compactação de resposta](xref:performance/response-compression) |
| **Conteúdo Estático**<br>`StaticFileModule` | Não | [Middleware de arquivos estáticos](xref:fundamentals/static-files) |
| **Cache de token**<br>`TokenCacheModule` | Sim | |
| **Cache de URI**<br>`UriCacheModule` | Sim | |
| **Autorização de URL**<br>`UrlAuthorizationModule` | Sim | [Identidade do ASP.NET Core](xref:security/authentication/identity) |
| **Autenticação do Windows**<br>`WindowsAuthenticationModule` | Sim | |

&#8224;Os tipos de correspondência `isFile` e `isDirectory` do módulo de regravação da URL não funcionam com aplicativos do ASP.NET Core, devido a alterações na [estrutura de diretórios](xref:host-and-deploy/directory-structure).

## <a name="managed-modules"></a>Módulos gerenciados

Os módulos gerenciados *não* funcionam com aplicativos do ASP.NET Core hospedados quando a versão do .NET CLR do pool de aplicativos está definido como **Sem Código Gerenciado**. O ASP.NET Core oferece alternativas de middleware em vários casos.

| Módulo                  | Opção do ASP.NET Core |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| FormsAuthentication     | [Middleware de autenticação de cookie](xref:security/authentication/cookie) |
| OutputCache             | [Middleware de Cache de Resposta](xref:performance/caching/middleware) |
| Perfil                 | |
| RoleManager             | |
| ScriptModule-4.0        | |
| Session                 | [Middleware de sessão](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [Middleware de regravação de URL](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4.0    | [Identidade do ASP.NET Core](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>Alterações de aplicativo do Gerenciador do IIS

Ao usar o Gerenciador do IIS para definir as configurações, o arquivo *web.config* do aplicativo é alterado. Ao implantar um aplicativo e incluir *web.config*, todas as alterações feitas com o Gerenciador do IIS são substituídas pelo arquivo *web.config* implantado. Se forem feitas alterações para o arquivo *web.config* do servidor, copie o arquivo *web.config* atualizado no servidor para o projeto local imediatamente.

## <a name="disabling-iis-modules"></a>Desabilitando módulos do IIS

Se um módulo do IIS é configurado no nível do servidor que deve ser desabilitado para um aplicativo, uma adição ao arquivo *web.config* do aplicativo pode desabilitar o módulo. Deixe o módulo no lugar e desative-o usando uma definição de configuração (se disponível) ou remova o módulo do aplicativo.

### <a name="module-deactivation"></a>Desativação do módulo

Muitos módulos oferecem uma configuração que permite que eles sejam desabilitados sem remover o módulo do aplicativo. Essa é a maneira mais simples e rápida de desativar um módulo. Por exemplo, o módulo de redirecionamento de HTTP pode ser desabilitado com o elemento  **\<httpRedirect>** em *web.config*:

```xml
<configuration>
  <system.webServer>
     <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Para obter mais informações sobre como desabilitar módulos com definições de configuração, siga os links na seção *Elementos Filho* de [IIS \<system.webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Remoção do módulo

Se optar pela remoção de um módulo com uma configuração em *web.config*, desbloqueie o módulo e desbloqueie a seção **\<modules>** de *web.config* primeiro:

1. Desbloqueie o módulo no nível do servidor. Selecione o servidor do IIS na barra lateral **Conexões** do Gerenciador do IIS. Abra os **Módulos** na área **IIS**. Selecione o módulo na lista. Na barra lateral **Ações** à direita, selecione **Desbloquear**. Desbloqueie todos os módulos que você planeja remover de *web.config* posteriormente.

2. Implantar o aplicativo sem uma seção **\<modules>** em *web.config*. Se um aplicativo é implantado com um *web.config* que contém a seção **\<modules>** sem ter desbloqueado a seção primeiro no Gerenciador do IIS, o Configuration Manager gera uma exceção ao tentar desbloquear a seção. Portanto, implante o aplicativo sem uma seção **\<modules>**.

3. Desbloqueie a seção **\<modules>** de *web.config*. Na barra lateral **Conexões**, selecione o site em **Sites**. Na área **Gerenciamento**, abra o **Editor de Configuração**. Use os controles de navegação para selecionar a seção `system.webServer/modules`. Na barra lateral **Ações** à direita, selecione para **Desbloquear** a seção.

4. Neste ponto, uma  **seção \<modules>** pode ser adicionada ao arquivo *web.config* com um elemento **\<remove>** para remover o módulo de o aplicativo. Vários elementos **\<remove>** podem ser adicionados para remover vários módulos. Se alterações a *web.config* forem feitas no servidor, faça imediatamente as mesmas alterações no arquivo *web.config* do projeto localmente. Remover um módulo dessa maneira não afetará o uso do módulo com outros aplicativos no servidor.

   ```xml
   <configuration> 
    <system.webServer> 
      <modules> 
        <remove name="MODULE_NAME" /> 
      </modules> 
    </system.webServer> 
   </configuration>
   ```

Um módulo do IIS também pode ser removido com *Appcmd.exe*. Forneça o `MODULE_NAME` e `APPLICATION_NAME` no comando:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Por exemplo, remova o `DynamicCompressionModule` do site da Web padrão:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Configuração do módulo mínimo

Os únicos módulos necessários para executar um aplicativo ASP.NET Core são o módulo de Autenticação Anônima e o Módulo do ASP.NET Core.

![Gerenciador do IIS aberto a módulos com a configuração de módulo mínima mostrada](modules/_static/modules.png)

O módulo de cache de URI (`UriCacheModule`) permite configuração de site, do IIS para o cache, no nível da URL. Sem esse módulo, o IIS deve ler e analisar a configuração em cada solicitação, mesmo quando a mesma URL é solicitada repetidamente. Analisar a configuração em cada solicitação resulta em uma perda de desempenho significativa. *Embora o módulo de cache de URI não seja estritamente necessário para que um aplicativo ASP.NET Core hospedado seja executado, é recomendável que o módulo de cache de URI seja habilitado para todas as implantações do ASP.NET Core.*

O módulo de cache HTTP (`HttpCacheModule`) implementa o cache de saída do IIS e também a lógica para gravar itens no cache do HTTP.sys. Sem esse módulo, o conteúdo não é mais armazenado em cache no modo kernel e perfis de cache são ignorados. Remover o módulo de cache HTTP geralmente tem um efeito adverso sobre o desempenho e o uso de recursos. *Embora o módulo de cache HTTP não seja estritamente necessário para que um aplicativo ASP.NET Core hospedado seja executado, é recomendável que o módulo de cache de HTTP seja habilitado para todas as implantações do ASP.NET Core.*

## <a name="additional-resources"></a>Recursos adicionais

* [Hospedar no Windows com o IIS](xref:host-and-deploy/iis/index)
* [Introdução às arquiteturas do IIS: módulos no IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Visão geral de módulos do IIS](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Personalizando funções e módulos do IIS 7.0](https://technet.microsoft.com/library/cc627313.aspx)
* [IIS `<system.webServer>`](/iis/configuration/system.webServer/)
