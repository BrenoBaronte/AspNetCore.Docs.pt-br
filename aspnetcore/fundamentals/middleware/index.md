---
title: Middleware do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o middleware do ASP.NET Core e o pipeline de solicitação.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: aa51e53284bc25629b3975ff0e6de967b9a2b866
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513116"
---
# <a name="aspnet-core-middleware"></a>Middleware do ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

O middleware é um software montado em um pipeline de aplicativo para manipular solicitações e respostas. Cada componente:

* Escolhe se deseja passar a solicitação para o próximo componente no pipeline.
* Pode executar o trabalho antes e depois do próximo componente no pipeline.

Os delegados de solicitação são usados para criar o pipeline de solicitação. Os delegados de solicitação manipulam cada solicitação HTTP.

Os delegados de solicitação são configurados usando os métodos de extensão <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> e <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>. Um delegado de solicitação individual pode ser especificado em linha como um método anônimo (chamado do middleware em linha) ou pode ser definido em uma classe reutilizável. Essas classes reutilizáveis e os métodos anônimos em linha são o *middleware*, também chamado de *componentes do middleware*. Cada componente de middleware no pipeline de solicitação é responsável por invocar o próximo componente no pipeline ou causar um curto-circuito do pipeline. Quando um middleware causa um curto-circuito, ele é chamado de *middleware terminal*, porque impede que outros middlewares processem a solicitação.

<xref:migration/http-modules> explica a diferença entre pipelines de solicitação no ASP.NET Core e no ASP.NET 4.x e fornece mais exemplos de middleware.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Criar um pipeline do middleware com o IApplicationBuilder

O pipeline de solicitação do ASP.NET Core consiste em uma sequência de delegados de solicitação, chamados um após o outro. O diagrama a seguir demonstra o conceito. O thread de execução segue as setas pretas.

![Padrão de processamento de solicitação mostrando a chegada de uma solicitação, processada por meio de três middlewares e a resposta que sai do aplicativo. Cada middleware executa sua lógica e transmite a solicitação para o próximo middleware na instrução next(). Depois que o terceiro middleware processa a solicitação, ela é transmitida por meio dos dois middlewares anteriores na ordem inversa para processamento adicional após suas instruções next(), antes de deixar o aplicativo como uma resposta ao cliente.](index/_static/request-delegate-pipeline.png)

Cada delegado pode executar operações antes e depois do próximo delegado. Os delegados de tratamento de exceção devem ser chamados no início do pipeline para que possam detectar exceções que ocorrem em etapas posteriores do pipeline.

O aplicativo ASP.NET Core mais simples possível define um delegado de solicitação única que controla todas as solicitações. Este caso não inclui um pipeline de solicitação real. Em vez disso, uma única função anônima é chamada em resposta a cada solicitação HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Encadeie vários delegados de solicitação junto com o <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>. O parâmetro `next` representa o próximo delegado no pipeline. Lembre-se de que você pode causar um curto-circuito no pipeline ao *não* chamar o parâmetro *next*. Normalmente, você pode executar ações antes e depois do próximo delegado, conforme o exemplo a seguir demonstra:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Quando um delegado não transmite uma solicitação ao próximo delegado, considera-se que ele esteja *causando um curto-circuito do pipeline de solicitação*. O curto-circuito geralmente é desejável porque ele evita trabalho desnecessário. Por exemplo, o [Middleware de Arquivo Estático](xref:fundamentals/static-files) pode agir com um *middleware terminal*, processando uma solicitação para um arquivo estático e causar curto-circuito no restante do pipeline. O middleware adicionado ao pipeline antes do middleware que finaliza o processamento adicional ainda processa o código após as instruções `next.Invoke`. No entanto, confira o seguinte aviso sobre a tentativa de gravar em uma resposta que já foi enviada.

> [!WARNING]
> Não chame `next.Invoke` depois que a resposta tiver sido enviada ao cliente. Altera para <xref:Microsoft.AspNetCore.Http.HttpResponse> depois de a resposta ser iniciada e lança uma exceção. Por exemplo, [a definição de cabeçalhos e um código de status geram uma exceção](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started). Gravar no corpo da resposta após a chamada `next`:
>
> * Pode causar uma violação do protocolo. Por exemplo, gravar mais do que o `Content-Length` indicado.
> * Pode corromper o formato do corpo. Por exemplo, gravar um rodapé HTML em um arquivo CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> é uma dica útil para indicar se os cabeçalhos foram enviados ou o corpo foi gravado.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> os delegados não recebem um `next` parâmetro. O primeiro `Run` delegado é sempre terminal e encerra o pipeline. `Run` é uma convenção. Alguns componentes de middleware podem expor `Run[Middleware]` métodos que são executados no final do pipeline:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

No exemplo anterior, o `Run` delegado grava na `"Hello from 2nd delegate."` resposta e, em seguida, encerra o pipeline. Se outro `Use` ou `Run` delegado for adicionado após o `Run` delegado, ele não será chamado.

<a name="order"></a>

## <a name="middleware-order"></a>Ordem de middleware

O diagrama a seguir mostra o pipeline de processamento de solicitação completo para ASP.NET Core aplicativos MVC e Razor pages. Você pode ver como, em um aplicativo típico, os middleware existentes são ordenados e onde middleware personalizados são adicionados. Você tem controle total sobre como reordenar middleware existentes ou injetar novos middleware personalizados conforme necessário para seus cenários.

![Pipeline de middleware ASP.NET Core](index/_static/middleware-pipeline.svg)

O middleware do **ponto de extremidade** no diagrama anterior executa o pipeline de filtro para as &mdash; páginas ou MVC do tipo de aplicativo correspondente Razor .

![Pipeline de filtro de ASP.NET Core](index/_static/mvc-endpoint.svg)

A ordem em que os componentes do middleware são adicionados ao método `Startup.Configure` define a ordem em que os componentes de middleware são invocados nas solicitações e a ordem inversa para a resposta. O pedido é **essencial** para segurança, desempenho e funcionalidade.

O método a seguir `Startup.Configure` adiciona componentes de middleware relacionados à segurança na ordem recomendada típica:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

No código anterior:

* O middleware que não é adicionado ao criar um novo aplicativo Web com [contas de usuários individuais](xref:security/authentication/identity) é comentado.
* Nem todo middleware precisa ir nessa ordem exata, mas muitos têm. Por exemplo:
  * `UseCors`, `UseAuthentication` e `UseAuthorization` devem ir na ordem mostrada.
  * `UseCors` no momento, deve ir antes `UseResponseCaching` devido a [esse bug](https://github.com/dotnet/aspnetcore/issues/23218).

Em alguns cenários, o middleware terá ordenação diferente. Por exemplo, a ordenação de cache e de compactação é específica do cenário, e há várias ordens válidas. Por exemplo:

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

Com o código anterior, a CPU poderia ser salva armazenando em cache a resposta compactada, mas você pode acabar armazenando em cache várias representações de um recurso usando diferentes algoritmos de compactação, como gzip ou brotli.

A ordenação a seguir combina arquivos estáticos para permitir o Caching de arquivos estáticos compactados:

```csharp
app.UseResponseCaching
app.UseResponseCompression
app.UseStaticFiles
```

O método `Startup.Configure` a seguir adiciona componentes de middleware para cenários de aplicativo comuns:

1. Exceção/tratamento de erro
   * Quando o aplicativo é executado no ambiente de desenvolvimento:
     * O middleware da página de exceção do desenvolvedor (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) relata erros de runtime do aplicativo.
     * Erro de banco de dados os relatórios de tempo de execução do banco de dados.
   * Quando o aplicativo é executado no ambiente de produção:
     * O middleware do manipulador de exceção (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) captura exceções geradas nos middlewares a seguir.
     * O middleware do protocolo HTTP Strict Transport Security (HSTS) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adiciona o cabeçalho `Strict-Transport-Security`.
1. O middleware de redirecionamento para HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redireciona as solicitações HTTP para HTTPS.
1. O middleware de arquivo estático (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) retorna arquivos estáticos e impede o processamento de novas solicitações.
1. Cookie O middleware de política ( <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A> ) está de acordo com as regulamentações de regulamento geral sobre a proteção de dados da UE (GDPR).
1. Middleware de roteamento ( <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ) para rotear solicitações.
1. O middleware de autenticação (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) tenta autenticar o usuário antes de ele ter acesso aos recursos seguros.
1. O middleware de autorização ( <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ) autoriza um usuário a acessar recursos seguros.
1. O middleware de sessão (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) estabelece e mantém o estado de sessão. Se o aplicativo usar o estado de sessão, chame middleware de sessão após o Cookie middleware de política e antes do MIDDLEWARE MVC.
1. Middleware de roteamento de ponto de extremidade ( <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> com <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A> ) para adicionar Razor pontos de extremidade de páginas ao pipeline de solicitação.

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

No código de exemplo anterior, cada método de extensão de middleware é exposto em <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> por meio do namespace <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> é o primeiro componente de middleware adicionado ao pipeline. Portanto, o middleware de manipulador de exceção captura todas as exceções que ocorrem em chamadas posteriores.

O Middleware de Arquivo Estático é chamado no início do pipeline para que possa controlar as solicitações e causar o curto-circuito sem passar pelos componentes restantes. O middleware de arquivo estático **não** fornece verificações de autorização. Todos os arquivos servidos pelo middleware de arquivo estático, incluindo aqueles em *wwwroot*, estão disponíveis publicamente. Para conhecer uma abordagem para proteger arquivos estáticos, veja <xref:fundamentals/static-files>.

Se a solicitação não for controlada pelo Middleware de Arquivo Estático, ela será transmitida para o Middleware de Autenticação (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), que executa a autenticação. A autenticação causa curto-circuito em solicitações não autenticadas. Embora o middleware de autenticação autentique solicitações, a autorização (e rejeição) ocorra somente depois que o MVC selecionar uma Razor página específica ou um controlador MVC e uma ação.

O exemplo a seguir demonstra uma solicitação de middleware cujas solicitações de arquivos estáticos são manipuladas pelo Middleware de Arquivo Estático antes do Middleware de Compactação de Resposta. Arquivos estáticos não são compactados com este pedido de middleware. As Razor respostas das páginas podem ser compactadas.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

Para aplicativos de página única (SPAs), o middleware SPA <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> geralmente vem por último no pipeline de middleware. O middleware SPA vem por último:

* Para permitir que todos os outros middleware respondam primeiro a solicitações correspondentes.
* Para permitir que o SPAs com roteamento do lado do cliente seja executado para todas as rotas que não são reconhecidas pelo aplicativo do servidor.

Para obter mais detalhes sobre o SPAs, consulte os guias para os modelos de projeto [reagir](xref:spa/react) e [angular](xref:spa/angular) .

### <a name="forwarded-headers-middleware-order"></a>Ordem de middleware de cabeçalhos encaminhados

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a>Ramificar o pipeline de middleware

As extensões <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> são usadas como uma convenção de ramificação do pipeline. `Map` ramifica o pipeline de solicitação com base na correspondência do caminho da solicitação em questão. Se o caminho da solicitação iniciar com o caminho especificado, o branch será executado.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

A tabela a seguir mostra as solicitações e as respostas de `http://localhost:1234` usando o código anterior.

| Solicitação             | Resposta                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Saudação do delegado diferente de Map. |
| localhost:1234/map1 | Teste de Map 1                   |
| localhost:1234/map2 | Teste de Map 2                   |
| localhost:1234/map3 | Saudação do delegado diferente de Map. |

Ao usar `Map`, os segmentos de caminho correspondentes são removidos de `HttpRequest.Path` e anexados a `HttpRequest.PathBase` para cada solicitação.

`Map` é compatível com aninhamento, por exemplo:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` também pode ser correspondido com vários segmentos de uma vez:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> ramifica o pipeline de solicitação com base no resultado do predicado fornecido. Qualquer predicado do tipo `Func<HttpContext, bool>` pode ser usado para mapear as solicitações para um novo branch do pipeline. No exemplo a seguir, um predicado é usado para detectar a presença de uma variável de cadeia de caracteres de consulta `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

A tabela a seguir mostra as solicitações e as respostas de `http://localhost:1234` usando o código anterior:

| Solicitação                       | Resposta                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Saudação do delegado diferente de Map. |
| localhost:1234/?branch=master | Branch usado = mestre         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> também ramifica o pipeline de solicitação com base no resultado do predicado fornecido. Ao contrário `MapWhen` do com o, esse Branch será reassociado ao pipeline principal se ele não for curto-Circuit ou contiver um middleware de terminal:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

No exemplo anterior, uma resposta de "Olá do pipeline principal". é gravado para todas as solicitações. Se a solicitação incluir uma variável de cadeia de caracteres de consulta `branch` , seu valor será registrado antes que o pipeline principal seja reassociado.

## <a name="built-in-middleware"></a>Middleware interno

O ASP.NET Core é fornecido com os seguintes componentes de middleware. A coluna *Ordem* fornece observações sobre o posicionamento do middleware no pipeline de processamento da solicitação e sob quais condições o middleware podem encerrar o processamento da solicitação. Quando um middleware causa um curto-circuito na solicitação ao processar o pipeline e impede outros middleware downstream de processar uma solicitação, ele é chamado de *middleware terminal*. Para saber mais sobre curto-circuito, confira a seção [Criar um pipeline de middleware com o IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Middleware | Descrição | Order |
| ---------- | ----------- | ----- |
| [Autenticação](xref:security/authentication/identity) | Fornece suporte à autenticação. | Antes de `HttpContext.User` ser necessário. Terminal para retornos de chamada OAuth. |
| [Autorização](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | Fornece suporte à autorização. | Imediatamente após o middleware de autenticação. |
| [Cookie Regras](xref:security/gdpr) | Rastreia o consentimento dos usuários para armazenar informações pessoais e impõe padrões mínimos para cookie campos, como `secure` e `SameSite` . | Antes do middleware que emite cookie s. Exemplos: Autenticação, Sessão e MVC (TempData). |
| [CORS](xref:security/cors) | Configura o Compartilhamento de Recursos entre Origens. | Antes de componentes que usam o CORS. `UseCors` no momento, deve ir antes `UseResponseCaching` devido a [esse bug](https://github.com/dotnet/aspnetcore/issues/23218).|
| [Diagnóstico](xref:fundamentals/error-handling) | Vários middleware separados que fornecem uma página de exceção do desenvolvedor, tratamento de exceção, páginas de código de status e a página da Web padrão para novos aplicativos. | Antes dos componentes que geram erros. Terminal para exceções ou para servir a página da Web padrão para novos aplicativos. |
| [Cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer) | Encaminha cabeçalhos como proxy para a solicitação atual. | Antes dos componentes que consomem os campos atualizados. Exemplos: esquema, host, IP do cliente e método. |
| [Verificação de integridade](xref:host-and-deploy/health-checks) | Verifica a integridade de um aplicativo ASP.NET Core e suas dependências, como a verificação da disponibilidade do banco de dados. | Terminal, se uma solicitação corresponde a um ponto de extremidade da verificação de integridade. |
| [Propagação de cabeçalho](xref:fundamentals/http-requests#header-propagation-middleware) | Propaga cabeçalhos HTTP da solicitação de entrada para as solicitações de cliente HTTP de saída. |
| [Substituição do Método HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Permite que uma solicitação de entrada POST substitua o método. | Antes dos componentes que consomem o método atualizado. |
| [Redirecionamento de HTTPS](xref:security/enforcing-ssl#require-https) | Redirecione todas as solicitações HTTP para HTTPS. | Antes dos componentes que consomem a URL. |
| [Segurança de Transporte Estrita de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware de aprimoramento de segurança que adiciona um cabeçalho de resposta especial. | Antes das respostas serem enviadas e depois dos componentes que modificam solicitações. Exemplos: Cabeçalhos encaminhados, regravação de URL. |
| [MVC](xref:mvc/overview) | Processa solicitações com MVC/ Razor páginas. | Terminal, se uma solicitação corresponder a uma rota. |
| [OWIN](xref:fundamentals/owin) | Interoperabilidade com aplicativos baseados em OWIN, em servidores e em middleware. | Terminal, se o middleware OWIN processa totalmente a solicitação. |
| [Cache de resposta](xref:performance/caching/middleware) | Fornece suporte para as respostas em cache. | Antes dos componentes que exigem armazenamento em cache. `UseCORS` deve vir antes `UseResponseCaching` .|
| [Compactação de resposta](xref:performance/response-compression) | Fornece suporte para a compactação de respostas. | Antes dos componentes que exigem compactação. |
| [Localização de Solicitação](xref:fundamentals/localization) | Fornece suporte à localização. | Antes dos componentes de localização importantes. |
| [Roteamento de ponto de extremidade](xref:fundamentals/routing) | Define e restringe as rotas de solicitação. | Terminal de rotas correspondentes. |
| [AUTENTICAÇÃO](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | Lida com todas as solicitações desse ponto na cadeia de middleware retornando a página padrão para o aplicativo de página única (SPA) | No final da cadeia, para que outro middleware para servir arquivos estáticos, as ações do MVC, etc., tem precedência.|
| [Sessão](xref:fundamentals/app-state) | Fornece suporte para gerenciar sessões de usuário. | Antes de componentes que exigem a sessão. | 
| [Arquivos estáticos](xref:fundamentals/static-files) | Fornece suporte para servir arquivos estáticos e pesquisa no diretório. | Terminal, se uma solicitação corresponde a um arquivo. |
| [Regravação de URL](xref:fundamentals/url-rewriting) | Fornece suporte para regravar URLs e redirecionar solicitações. | Antes dos componentes que consomem a URL. |
| [WebSockets](xref:fundamentals/websockets) | Habilita o protocolo WebSockets. | Antes dos componentes que são necessários para aceitar solicitações de WebSocket. |

## <a name="additional-resources"></a>Recursos adicionais

* [As opções de tempo de vida e de registro](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contêm um exemplo completo de middleware com serviços de tempo de vida com *escopo*, *transitório* e *singleton* .
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

O middleware é um software montado em um pipeline de aplicativo para manipular solicitações e respostas. Cada componente:

* Escolhe se deseja passar a solicitação para o próximo componente no pipeline.
* Pode executar o trabalho antes e depois do próximo componente no pipeline.

Os delegados de solicitação são usados para criar o pipeline de solicitação. Os delegados de solicitação manipulam cada solicitação HTTP.

Os delegados de solicitação são configurados usando os métodos de extensão <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> e <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>. Um delegado de solicitação individual pode ser especificado em linha como um método anônimo (chamado do middleware em linha) ou pode ser definido em uma classe reutilizável. Essas classes reutilizáveis e os métodos anônimos em linha são o *middleware*, também chamado de *componentes do middleware*. Cada componente de middleware no pipeline de solicitação é responsável por invocar o próximo componente no pipeline ou causar um curto-circuito do pipeline. Quando um middleware causa um curto-circuito, ele é chamado de *middleware terminal*, porque impede que outros middlewares processem a solicitação.

<xref:migration/http-modules> explica a diferença entre pipelines de solicitação no ASP.NET Core e no ASP.NET 4.x e fornece mais exemplos de middleware.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Criar um pipeline do middleware com o IApplicationBuilder

O pipeline de solicitação do ASP.NET Core consiste em uma sequência de delegados de solicitação, chamados um após o outro. O diagrama a seguir demonstra o conceito. O thread de execução segue as setas pretas.

![Padrão de processamento de solicitação mostrando a chegada de uma solicitação, processada por meio de três middlewares e a resposta que sai do aplicativo. Cada middleware executa sua lógica e transmite a solicitação para o próximo middleware na instrução next(). Depois que o terceiro middleware processa a solicitação, ela é transmitida por meio dos dois middlewares anteriores na ordem inversa para processamento adicional após suas instruções next(), antes de deixar o aplicativo como uma resposta ao cliente.](index/_static/request-delegate-pipeline.png)

Cada delegado pode executar operações antes e depois do próximo delegado. Os delegados de tratamento de exceção devem ser chamados no início do pipeline para que possam detectar exceções que ocorrem em etapas posteriores do pipeline.

O aplicativo ASP.NET Core mais simples possível define um delegado de solicitação única que controla todas as solicitações. Este caso não inclui um pipeline de solicitação real. Em vez disso, uma única função anônima é chamada em resposta a cada solicitação HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

O primeiro delegado <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> encerra o pipeline.

Encadeie vários delegados de solicitação junto com o <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>. O parâmetro `next` representa o próximo delegado no pipeline. Lembre-se de que você pode causar um curto-circuito no pipeline ao *não* chamar o parâmetro *next*. Normalmente, você pode executar ações antes e depois do próximo delegado, conforme o exemplo a seguir demonstra:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Quando um delegado não transmite uma solicitação ao próximo delegado, considera-se que ele esteja *causando um curto-circuito do pipeline de solicitação*. O curto-circuito geralmente é desejável porque ele evita trabalho desnecessário. Por exemplo, o [Middleware de Arquivo Estático](xref:fundamentals/static-files) pode agir com um *middleware terminal*, processando uma solicitação para um arquivo estático e causar curto-circuito no restante do pipeline. O middleware adicionado ao pipeline antes do middleware que finaliza o processamento adicional ainda processa o código após as instruções `next.Invoke`. No entanto, confira o seguinte aviso sobre a tentativa de gravar em uma resposta que já foi enviada.

> [!WARNING]
> Não chame `next.Invoke` depois que a resposta tiver sido enviada ao cliente. Altera para <xref:Microsoft.AspNetCore.Http.HttpResponse> depois de a resposta ser iniciada e lança uma exceção. Por exemplo, mudanças como a configuração de cabeçalhos e o código de status lançam uma exceção. Gravar no corpo da resposta após a chamada `next`:
>
> * Pode causar uma violação do protocolo. Por exemplo, gravar mais do que o `Content-Length` indicado.
> * Pode corromper o formato do corpo. Por exemplo, gravar um rodapé HTML em um arquivo CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> é uma dica útil para indicar se os cabeçalhos foram enviados ou o corpo foi gravado.

<a name="order"></a>

## <a name="middleware-order"></a>Ordem de middleware

A ordem em que os componentes do middleware são adicionados ao método `Startup.Configure` define a ordem em que os componentes de middleware são invocados nas solicitações e a ordem inversa para a resposta. O pedido é **essencial** para segurança, desempenho e funcionalidade.

O método a seguir `Startup.Configure` adiciona os componentes de middleware relacionados à segurança na ordem recomendada:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

No código anterior:

* O middleware que não é adicionado ao criar um novo aplicativo Web com [contas de usuários individuais](xref:security/authentication/identity) é comentado.
* Nem todo middleware precisa ir nessa ordem exata, mas muitos têm. Por exemplo, `UseCors` e `UseAuthentication` deve ir na ordem mostrada.

O método `Startup.Configure` a seguir adiciona componentes de middleware para cenários de aplicativo comuns:

1. Exceção/tratamento de erro
   * Quando o aplicativo é executado no ambiente de desenvolvimento:
     * O middleware da página de exceção do desenvolvedor (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) relata erros de runtime do aplicativo.
     * O middleware da página de erro do banco de dados (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) relata erros de runtime do banco de dados.
   * Quando o aplicativo é executado no ambiente de produção:
     * O middleware do manipulador de exceção (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) captura exceções geradas nos middlewares a seguir.
     * O middleware do protocolo HTTP Strict Transport Security (HSTS) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adiciona o cabeçalho `Strict-Transport-Security`.
1. O middleware de redirecionamento para HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redireciona as solicitações HTTP para HTTPS.
1. O middleware de arquivo estático (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) retorna arquivos estáticos e impede o processamento de novas solicitações.
1. Cookie O middleware de política ( <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A> ) está de acordo com as regulamentações de regulamento geral sobre a proteção de dados da UE (GDPR).
1. O middleware de autenticação (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) tenta autenticar o usuário antes de ele ter acesso aos recursos seguros.
1. O middleware de sessão (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) estabelece e mantém o estado de sessão. Se o aplicativo usar o estado de sessão, chame middleware de sessão após o Cookie middleware de política e antes do MIDDLEWARE MVC.
1. MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) para adicionar o MVC ao pipeline de solicitação.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

No código de exemplo anterior, cada método de extensão de middleware é exposto em <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> por meio do namespace <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> é o primeiro componente de middleware adicionado ao pipeline. Portanto, o middleware de manipulador de exceção captura todas as exceções que ocorrem em chamadas posteriores.

O Middleware de Arquivo Estático é chamado no início do pipeline para que possa controlar as solicitações e causar o curto-circuito sem passar pelos componentes restantes. O middleware de arquivo estático **não** fornece verificações de autorização. Todos os arquivos servidos pelo middleware de arquivo estático, incluindo aqueles em *wwwroot*, estão disponíveis publicamente. Para conhecer uma abordagem para proteger arquivos estáticos, veja <xref:fundamentals/static-files>.

Se a solicitação não for controlada pelo Middleware de Arquivo Estático, ela será transmitida para o Middleware de Autenticação (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), que executa a autenticação. A autenticação causa curto-circuito em solicitações não autenticadas. Embora o middleware de autenticação autentique solicitações, a autorização (e rejeição) ocorra somente depois que o MVC selecionar uma Razor página específica ou um controlador MVC e uma ação.

O exemplo a seguir demonstra uma solicitação de middleware cujas solicitações de arquivos estáticos são manipuladas pelo Middleware de Arquivo Estático antes do Middleware de Compactação de Resposta. Arquivos estáticos não são compactados com este pedido de middleware. As respostas do MVC de <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> podem ser compactadas.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Use, Run e Map

Configure o pipeline de HTTP usando <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> e <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>. O método `Use` pode ligar o pipeline em curto-circuito (ou seja, se ele não chamar um delegado de solicitação `next`). `Run` é uma convenção e alguns componentes de middleware podem expor os métodos `Run[Middleware]` que são executados no final do pipeline.

As extensões <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> são usadas como uma convenção de ramificação do pipeline. `Map` ramifica o pipeline de solicitação com base na correspondência do caminho da solicitação em questão. Se o caminho da solicitação iniciar com o caminho especificado, o branch será executado.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

A tabela a seguir mostra as solicitações e as respostas de `http://localhost:1234` usando o código anterior.

| Solicitação             | Resposta                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Saudação do delegado diferente de Map. |
| localhost:1234/map1 | Teste de Map 1                   |
| localhost:1234/map2 | Teste de Map 2                   |
| localhost:1234/map3 | Saudação do delegado diferente de Map. |

Ao usar `Map`, os segmentos de caminho correspondentes são removidos de `HttpRequest.Path` e anexados a `HttpRequest.PathBase` para cada solicitação.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> ramifica o pipeline de solicitação com base no resultado do predicado fornecido. Qualquer predicado do tipo `Func<HttpContext, bool>` pode ser usado para mapear as solicitações para um novo branch do pipeline. No exemplo a seguir, um predicado é usado para detectar a presença de uma variável de cadeia de caracteres de consulta `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

A tabela a seguir mostra as solicitações e as respostas de `http://localhost:1234` usando o código anterior.

| Solicitação                       | Resposta                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Saudação do delegado diferente de Map. |
| localhost:1234/?branch=master | Branch usado = mestre         |

`Map` é compatível com aninhamento, por exemplo:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` também pode ser correspondido com vários segmentos de uma vez:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Middleware interno

O ASP.NET Core é fornecido com os seguintes componentes de middleware. A coluna *Ordem* fornece observações sobre o posicionamento do middleware no pipeline de processamento da solicitação e sob quais condições o middleware podem encerrar o processamento da solicitação. Quando um middleware causa um curto-circuito na solicitação ao processar o pipeline e impede outros middleware downstream de processar uma solicitação, ele é chamado de *middleware terminal*. Para saber mais sobre curto-circuito, confira a seção [Criar um pipeline de middleware com o IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Middleware | Descrição | Order |
| ---------- | ----------- | ----- |
| [Autenticação](xref:security/authentication/identity) | Fornece suporte à autenticação. | Antes de `HttpContext.User` ser necessário. Terminal para retornos de chamada OAuth. |
| [Cookie Regras](xref:security/gdpr) | Rastreia o consentimento dos usuários para armazenar informações pessoais e impõe padrões mínimos para cookie campos, como `secure` e `SameSite` . | Antes do middleware que emite cookie s. Exemplos: Autenticação, Sessão e MVC (TempData). |
| [CORS](xref:security/cors) | Configura o Compartilhamento de Recursos entre Origens. | Antes de componentes que usam o CORS. |
| [Diagnóstico](xref:fundamentals/error-handling) | Vários middleware separados que fornecem uma página de exceção do desenvolvedor, tratamento de exceção, páginas de código de status e a página da Web padrão para novos aplicativos. | Antes dos componentes que geram erros. Terminal para exceções ou para servir a página da Web padrão para novos aplicativos. |
| [Cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer) | Encaminha cabeçalhos como proxy para a solicitação atual. | Antes dos componentes que consomem os campos atualizados. Exemplos: esquema, host, IP do cliente e método. |
| [Verificação de integridade](xref:host-and-deploy/health-checks) | Verifica a integridade de um aplicativo ASP.NET Core e suas dependências, como a verificação da disponibilidade do banco de dados. | Terminal, se uma solicitação corresponde a um ponto de extremidade da verificação de integridade. |
| [Substituição do Método HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Permite que uma solicitação de entrada POST substitua o método. | Antes dos componentes que consomem o método atualizado. |
| [Redirecionamento de HTTPS](xref:security/enforcing-ssl#require-https) | Redirecione todas as solicitações HTTP para HTTPS. | Antes dos componentes que consomem a URL. |
| [Segurança de Transporte Estrita de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware de aprimoramento de segurança que adiciona um cabeçalho de resposta especial. | Antes das respostas serem enviadas e depois dos componentes que modificam solicitações. Exemplos: Cabeçalhos encaminhados, regravação de URL. |
| [MVC](xref:mvc/overview) | Processa solicitações com MVC/ Razor páginas. | Terminal, se uma solicitação corresponder a uma rota. |
| [OWIN](xref:fundamentals/owin) | Interoperabilidade com aplicativos baseados em OWIN, em servidores e em middleware. | Terminal, se o middleware OWIN processa totalmente a solicitação. |
| [Cache de resposta](xref:performance/caching/middleware) | Fornece suporte para as respostas em cache. | Antes dos componentes que exigem armazenamento em cache. |
| [Compactação de resposta](xref:performance/response-compression) | Fornece suporte para a compactação de respostas. | Antes dos componentes que exigem compactação. |
| [Localização de Solicitação](xref:fundamentals/localization) | Fornece suporte à localização. | Antes dos componentes de localização importantes. |
| [Roteamento de ponto de extremidade](xref:fundamentals/routing) | Define e restringe as rotas de solicitação. | Terminal de rotas correspondentes. |
| [Sessão](xref:fundamentals/app-state) | Fornece suporte para gerenciar sessões de usuário. | Antes de componentes que exigem a sessão. |
| [Arquivos estáticos](xref:fundamentals/static-files) | Fornece suporte para servir arquivos estáticos e pesquisa no diretório. | Terminal, se uma solicitação corresponde a um arquivo. |
| [Regravação de URL](xref:fundamentals/url-rewriting) | Fornece suporte para regravar URLs e redirecionar solicitações. | Antes dos componentes que consomem a URL. |
| [WebSockets](xref:fundamentals/websockets) | Habilita o protocolo WebSockets. | Antes dos componentes que são necessários para aceitar solicitações de WebSocket. |

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
