---
title: Roteamento no ASP.NET Core
author: rick-anderson
description: Descubra como ASP.NET roteamento Core é responsável por corresponder solicitações HTTP e despachar para pontos finais executáveis.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
uid: fundamentals/routing
ms.openlocfilehash: 5742ac6879ce46e01247ddd2f8bfe3e3b8a2a02a
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751154"
---
# <a name="routing-in-aspnet-core"></a>Roteamento no ASP.NET Core

Por [Ryan Nowak,](https://github.com/rynowak) [Kirk Larkin](https://twitter.com/serpent5)e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

O roteamento é responsável por corresponder às solicitações HTTP recebidas e despachar essas solicitações para os pontos finais executáveis do aplicativo. [Os pontos finais](#endpoint) são as unidades do aplicativo de código executável de tratamento de solicitações. Os pontos finais são definidos no aplicativo e configurados quando o aplicativo é iniciado. O processo de correspondência de ponto final pode extrair valores da URL da solicitação e fornecer esses valores para o processamento de solicitações. Usando informações de ponto final do aplicativo, o roteamento também é capaz de gerar URLs que mapeiam para pontos finais.

Os aplicativos podem configurar o roteamento usando:

- Controladores
- Páginas do Razor
- SignalR
- serviços gRPC
- Middleware habilitado [middleware](xref:fundamentals/middleware/index) para endpoint, como [verificações de saúde](xref:host-and-deploy/health-checks).
- Delegados e lambdas registrados com roteamento.

Este documento abrange detalhes de baixo nível do roteamento ASP.NET Core. Para obter informações sobre a configuração do roteamento:

* Para os controladores, veja <xref:mvc/controllers/routing>.
* Para convenções de <xref:razor-pages/razor-pages-conventions>Páginas de Barbear, veja .

O sistema de roteamento de ponto final descrito neste documento se aplica a ASP.NET Núcleo 3.0 e posterior. Para obter informações sobre o <xref:Microsoft.AspNetCore.Routing.IRouter>sistema de roteamento anterior baseado em , selecione a versão ASP.NET Core 2.1 usando uma das seguintes abordagens:

* O seletor de versão para uma versão anterior.
* Selecione [ASP.NET roteamento Core 2.1](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([como baixar](xref:index#how-to-download-a-sample))

As amostras de download para este `Startup` documento são habilitadas por uma classe específica. Para executar uma amostra *Program.cs* específica, modifique `Startup` Program.cs para chamar a classe desejada.

## <a name="routing-basics"></a>Conceitos básicos sobre roteamento

Todos os modelos ASP.NET Core incluem roteamento no código gerado. O roteamento é registrado no `Startup.Configure`pipeline [de middleware](xref:fundamentals/middleware/index) em .

O código a seguir mostra um exemplo básico de roteamento:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

O roteamento usa um par <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>middleware, registrados por e:

* `UseRouting`adiciona correspondência de rota ao pipeline de middleware. Este middleware analisa o conjunto de pontos finais definidos no aplicativo e seleciona a [melhor correspondência](#urlm) com base na solicitação.
* `UseEndpoints`adiciona execução de ponto final ao pipeline de middleware. Ele executa o delegado associado ao ponto final selecionado.

O exemplo anterior inclui uma única *rota para* o ponto final de código usando o método [MapGet:](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*)

* Quando uma `GET` solicitação HTTP é `/`enviada para a URL raiz:
  * O delegado de solicitação mostrado executa.
  * `Hello World!`é escrito para a resposta HTTP. Por padrão, a `/` `https://localhost:5001/`URL raiz é .
* Se o método `GET` de solicitação não `/`for ou a URL raiz não for, nenhuma rota corresponde e um HTTP 404 será devolvido.

### <a name="endpoint"></a>Ponto de extremidade

<a name="endpoint"></a>

O `MapGet` método é usado para definir um **ponto final**. Um ponto final é algo que pode ser:

* Selecionado, combinando o método URL e HTTP.
* Executado, executando o delegado.

Os pontos finais que podem ser combinados e `UseEndpoints`executados pelo aplicativo são configurados em . Por <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>exemplo, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> [métodos semelhantes](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) conectam os delegados de solicitação ao sistema de roteamento.
Métodos adicionais podem ser usados para conectar ASP.NET recursos da estrutura Core ao sistema de roteamento:
- [MapRazorPages para Páginas de Navalha](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [Controladores de mapas para controladores](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub\<THub> para SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService\<TService> para gRPC](xref:grpc/aspnetcore)

O exemplo a seguir mostra roteamento com um modelo de rota mais sofisticado:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

A `/hello/{name:alpha}` seqüência é um **modelo de rota**. Ele é usado para configurar como o ponto final é correspondido. Neste caso, o modelo corresponde:

* Uma URL como`/hello/Ryan`
* Qualquer caminho de `/hello/` URL que comece seguido por uma seqüência de caracteres alfabéticos.  `:alpha`aplica uma restrição de rota que corresponde apenas a caracteres alfabéticos. [As restrições de rota](#route-constraint-reference) são explicadas mais tarde neste documento.

O segundo segmento do `{name:alpha}`caminho da URL:

* Está ligado `name` ao parâmetro.
* É capturado e armazenado em [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).

O sistema de roteamento de ponto final descrito neste documento é novo a partir de ASP.NET Núcleo 3.0. No entanto, todas as versões do ASP.NET Core suportam o mesmo conjunto de recursos de modelo de rota e restrições de rota.

O exemplo a seguir mostra roteamento com [verificações de saúde](xref:host-and-deploy/health-checks) e autorização:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

O exemplo anterior demonstra como:

* O middleware de autorização pode ser usado com roteamento.
* Os pontos finais podem ser usados para configurar o comportamento de autorização.

A <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> chamada adiciona um ponto final de verificação de saúde. O <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> acorrentamento a esta chamada anexa uma política de autorização ao ponto final.

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> Chamando <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> e adiciona o middleware de autenticação e autorização. Esses middleware são <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> `UseEndpoints` colocados entre e para que possam:

* Veja qual ponto final `UseRouting`foi selecionado por .
* Aplique uma política <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> de autorização antes de despachar para o ponto final.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Metadados de ponto final

No exemplo anterior, há dois pontos finais, mas apenas o ponto final da verificação de saúde tem uma política de autorização anexada. Se a solicitação corresponder ao `/healthz`ponto final da verificação de saúde, uma verificação de autorização é realizada. Isso demonstra que os pontos finais podem ter dados extras anexados a eles. Esses dados extras são chamados **de metadados**de ponto final:

* Os metadados podem ser processados por middleware com reconhecimento de roteamento.
* Os metadados podem ser de qualquer tipo .NET.

## <a name="routing-concepts"></a>Conceitos de roteamento

O sistema de roteamento se constrói em cima do pipeline de middleware adicionando o poderoso conceito **de ponto final.** Os pontos finais representam unidades da funcionalidade do aplicativo que são distintas umas das outras em termos de roteamento, autorização e qualquer número de sistemas do ASP.NET Core.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>definição de ponto final do núcleo ASP.NET

Um ponto final ASP.NET é:

* Executável: Tem <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>um .
* Extensível: Tem uma coleção [de Metadados.](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*)
* Selecionável: Opcionalmente, tem [informações de roteamento](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Enumeração: A coleção de pontos finais pode <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> ser listada recuperando o [DI](xref:fundamentals/dependency-injection).

O código a seguir mostra como recuperar e inspecionar o ponto final correspondente à solicitação atual:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

O ponto final, se selecionado, pode `HttpContext`ser recuperado do . Suas propriedades podem ser inspecionadas. Objetos de ponto final são imutáveis e não podem ser modificados após a criação. O tipo mais comum <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>de ponto final é um . `RouteEndpoint`inclui informações que permitem que ele seja selecionado pelo sistema de roteamento.

No código anterior, [aplicativo. O uso](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configura um [middleware](xref:fundamentals/middleware/index)em linha .

<a name="mt"></a>

O código a seguir mostra `app.Use` que, dependendo de onde é chamado no pipeline, pode não haver um ponto final:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

Esta amostra `Console.WriteLine` anterior adiciona instruções que exibem se um ponto final foi selecionado ou não. Para obter clareza, a amostra atribui `/` um nome de exibição ao ponto final fornecido.

Executando este código com `/` uma URL de displays:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Executando este código com quaisquer outros displays de URL:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Esta saída demonstra que:

* O ponto final é `UseRouting` sempre nulo antes de ser chamado.
* Se uma correspondência for encontrada, o ponto `UseRouting` <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>final não será nulo entre e .
* O `UseEndpoints` middleware é **terminal** quando uma correspondência é encontrada. [O middleware terminal](#tm) é definido mais tarde neste documento.
* O middleware `UseEndpoints` após a execução somente quando nenhuma correspondência é encontrada.

O `UseRouting` middleware usa o método [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) para anexar o ponto final ao contexto atual. É possível substituir o `UseRouting` middleware por uma lógica personalizada e ainda obter os benefícios de usar endpoints. Os pontos finais são primitivos de baixo nível como middleware, e não estão acoplados à implementação de roteamento. A maioria dos aplicativos `UseRouting` não precisa ser substituída por uma lógica personalizada.

O `UseEndpoints` middleware foi projetado para ser usado `UseRouting` em conjunto com o middleware. A lógica central para executar um ponto final não é complicada. Use <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> para recuperar o ponto final <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> e, em seguida, invoque sua propriedade.

O código a seguir demonstra como o middleware pode influenciar ou reagir ao roteamento:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

O exemplo anterior demonstra dois conceitos importantes:

* O Middleware `UseRouting` pode ser executado antes para modificar os dados que o roteamento opera.
    * Normalmente, middleware que aparece antes do roteamento <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>modifica <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>alguma propriedade da solicitação, tais como , ou .
* O middleware `UseRouting` pode <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ser executado entre e processar os resultados do roteamento antes que o ponto final seja executado.
    * Middleware que `UseRouting` funciona `UseEndpoints`entre e:
      * Normalmente inspeciona metadados para entender os pontos finais.
      * Muitas vezes toma decisões `UseAuthorization` de `UseCors`segurança, como feito por e .
    * A combinação de middleware e metadados permite configurar políticas por ponto final.

O código anterior mostra um exemplo de um middleware personalizado que suporta políticas por ponto final. O middleware grava um registro de *auditoria* de acesso a dados confidenciais ao console. O middleware pode ser configurado para `AuditPolicyAttribute` *auditar* um ponto final com os metadados. Esta amostra demonstra um *padrão de opt-in* onde apenas os pontos finais marcados como sensíveis são auditados. É possível definir essa lógica ao contrário, auditando tudo o que não está marcado como seguro, por exemplo. O sistema de metadados de ponto final é flexível. Essa lógica pode ser projetada de qualquer maneira que se adapte ao caso de uso.

O código amostral anterior destina-se a demonstrar os conceitos básicos de endpoints. **A amostra não é destinada ao uso da produção.** Uma versão mais completa de um middleware *de log* de auditoria seria:

* Faça login em um arquivo ou banco de dados.
* Inclua detalhes como o usuário, endereço IP, nome do ponto final sensível e muito mais.

Os metadados `AuditPolicyAttribute` da política `Attribute` de auditoria são definidos como um uso mais fácil com frameworks baseados em classe, como controladores e SignalR. Ao usar *a rota para codificar:*

* Metadados é anexado a uma API de construtor.
* Os frameworks baseados em classe incluem todos os atributos no método e classe correspondentes ao criar pontos finais.

As práticas recomendadas para os tipos de metadados são defini-los como interfaces ou atributos. Interfaces e atributos permitem a reutilização de código. O sistema de metadados é flexível e não impõe limitações.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Comparando um middleware terminal e roteamento

A amostra de código a seguir contrasta usando middleware com o uso de roteamento:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

O estilo de middleware mostrado com `Approach 1:` é **middleware terminal**. Chama-se middleware terminal porque faz uma operação correspondente:

* A operação correspondente na `Path == "/"` amostra anterior é `Path == "/Movie"` para o middleware e para o roteamento.
* Quando uma correspondência é bem sucedida, ela executa algumas `next` funcionalidades e retorna, em vez de invocar o middleware.

Chama-se middleware terminal porque encerra a pesquisa, executa algumas funcionalidades e retorna.

Comparando um middleware terminal e roteamento:
* Ambas as abordagens permitem encerrar o pipeline de processamento:
    * Middleware termina o pipeline retornando em `next`vez de invocar .
    * Os pontos finais são sempre terminais.
* O middleware terminal permite posicionar o middleware em um lugar arbitrário no pipeline:
    * Os pontos finais são <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>executados na posição de .
* O middleware terminal permite que o código arbitrário determine quando o middleware corresponde:
    * O código de correspondência de rota personalizado pode ser verboso e difícil de escrever corretamente.
    * O roteamento fornece soluções simples para aplicativos típicos. A maioria dos aplicativos não requer código de correspondência de rota personalizado.
* Interface de endpoints com `UseAuthorization` `UseCors`middleware como e .
    * Utilização de um `UseAuthorization` middleware terminal com ou `UseCors` requer interação manual com o sistema de autorização.

Um [ponto final](#endpoint) define ambos:

* Um delegado para processar pedidos.
* Uma coleção de metadados arbitrários. Os metadados são usados para implementar preocupações transversais com base em políticas e configuração anexadas a cada ponto final.

O middleware terminal pode ser uma ferramenta eficaz, mas pode exigir:

* Uma quantidade significativa de codificação e testes.
* Integração manual com outros sistemas para alcançar o nível de flexibilidade desejado.

Considere integrar-se com o roteamento antes de escrever um middleware terminal.

O middleware de terminal [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) existente <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> que se integra ao Map ou geralmente pode ser transformado em um ponto final de reconhecimento de roteamento. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) demonstra o padrão de roteador::
* Escreva um método <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>de extensão em .
* Crie um pipeline de <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>middleware aninhado usando .
* Conecte o middleware ao novo oleoduto. Nesse caso, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>o pipeline de <xref:Microsoft.AspNetCore.Http.RequestDelegate>middleware em um .
* Ligue `Map` e forneça o novo oleoduto de middleware.
* Devolva o objeto `Map` construtor fornecido pelo método de extensão.

O código a seguir mostra o uso de [MapHealthChecks](xref:host-and-deploy/health-checks):

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

A amostra anterior mostra por que devolver o objeto construtor é importante. O retorno do objeto construtor permite que o desenvolvedor do aplicativo configure políticas como autorização para o ponto final. Neste exemplo, o middleware de verificação de saúde não tem integração direta com o sistema de autorização.

O sistema de metadados foi criado em resposta aos problemas encontrados pelos autores de extensibilidade usando middleware terminal. É problemático para cada middleware implementar sua própria integração com o sistema de autorização.

<a name="urlm"></a>

### <a name="url-matching"></a>Correspondência de URL

* É o processo pelo qual o roteamento corresponde a um pedido de entrada para um [ponto final](#endpoint).
* É baseado em dados no caminho url e cabeçalhos.
* Pode ser estendido para considerar qualquer dado na solicitação.

Quando um middleware de roteamento `Endpoint` é executado, ele define <xref:Microsoft.AspNetCore.Http.HttpContext> um e os valores de rota para um recurso de [solicitação](xref:fundamentals/request-features) na partir da solicitação atual:

* Chamar [httpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) obtém o ponto final.
* `HttpRequest.RouteValues` obtém a coleção de valores de rota.

[Middleware](xref:fundamentals/middleware/index) em execução após o middleware de roteamento pode inspecionar o ponto final e agir. Por exemplo, um middleware de autorização pode interrogar a coleta de metadados do ponto final para uma política de autorização. Depois que todos os middlewares no pipeline de processamento da solicitação forem executados, o representante do ponto de extremidade selecionado será invocado.

O sistema de roteamento no roteamento de ponto de extremidade é responsável por todas as decisões de expedição. Como o middleware aplica políticas com base no ponto final selecionado, é importante que:

* Qualquer decisão que possa afetar o despacho ou a aplicação de políticas de segurança é tomada dentro do sistema de roteamento.

> [!WARNING]
> Para retrocompatibilidade, quando um delegado de ponto final do Controller ou Razor Pages é executado, as propriedades do [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) são definidas como valores apropriados com base no processamento de solicitação realizado até agora.
>
> O `RouteContext` tipo será marcado como obsoleto em uma versão futura:
>
> * Migrar `RouteData.Values` `HttpRequest.RouteValues`para .
> * Migrar `RouteData.DataTokens` para recuperar [IDataTokensMetadados](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) a partir dos metadados do ponto final.

A correspondência de URL opera em um conjunto de fases configuráveis. Em cada fase, a saída é um conjunto de partidas. O conjunto de partidas pode ser reduzido ainda mais para a próxima fase. A implementação de roteamento não garante uma ordem de processamento para pontos finais correspondentes. **Todas as** partidas possíveis são processadas de uma só vez. As fases de correspondência de URL ocorrem na seguinte ordem. ASP.NET Core:

1. Processa o caminho de URL em relação ao conjunto de pontos finais e seus modelos de rota, coletando **todas as** correspondências.
1. Pega a lista anterior e remove correspondências que falham com as restrições de rota aplicadas.
1. Pega a lista anterior e remove as correspondências que falham no conjunto de instâncias [do MatcherPolicy.](xref:Microsoft.AspNetCore.Routing.MatcherPolicy)
1. Usa o [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) para tomar uma decisão final da lista anterior.

A lista de pontos finais é priorizada de acordo com:

* O [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* A [precedência do modelo de rota](#rtp)

Todos os pontos finais correspondentes são <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> processados em cada fase até que o seja atingido. A `EndpointSelector` fase final. Ele escolhe o ponto final de maior prioridade das partidas como a melhor partida. Se houver outras partidas com a mesma prioridade da melhor partida, uma exceção ambígua de partida é lançada.

A precedência da rota é calculada com base em um modelo de rota **mais específico** que recebe uma prioridade maior. Por exemplo, considere `/hello` os `/{message}`modelos e:

* Ambos correspondem `/hello`ao caminho da URL .
* `/hello`é mais específica e, portanto, maior prioridade.

Em geral, a precedência de rota faz um bom trabalho de escolher a melhor combinação para os tipos de esquemas de URL usados na prática. Use <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> somente quando necessário para evitar uma ambiguidade.

Devido aos tipos de extensibilidade proporcionados pelo roteamento, não é possível que o sistema de roteamento calcule antecipadamente as rotas ambíguas. Considere um exemplo, como `/{message:alpha}` os `/{message:int}`modelos de rota e:

* A `alpha` restrição corresponde apenas a caracteres alfabéticos.
* A `int` restrição corresponde apenas aos números.
* Esses modelos têm a mesma precedência de rota, mas não há uma única URL que ambos correspondam.
* Se o sistema de roteamento relatasse um erro de ambiguidade na inicialização, ele bloquearia esse caso de uso válido.

> [!WARNING]
>
> A ordem das <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> operações internas não influencia o comportamento do roteamento, com uma exceção. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>e <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> atribuir automaticamente um valor de ordem aos seus pontos finais com base na ordem que eles são invocados. Isso simula o comportamento de longa data dos controladores sem que o sistema de roteamento forneça as mesmas garantias que as implementações de roteamento mais antigas.
>
> Na implementação legado do roteamento, é possível implementar a extensibilidade de roteamento que tenha uma dependência da ordem em que as rotas são processadas. Roteamento de ponto final no ASP.NET Núcleo 3.0 e posteriores:
> 
> * Não tem um conceito de rotas.
> * Não fornece garantias de pedidos. Todos os pontos finais são processados ao mesmo tempo.
>
> Se isso significa que você está preso usando o sistema de roteamento legado, [abra um problema do GitHub para assistência](https://github.com/dotnet/aspnetcore/issues).

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Precedência do modelo de rota e ordem de seleção de ponto final

[Precedência do modelo de rota](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) é um sistema que atribui a cada modelo de rota um valor com base no quão específico ele é. Precedência do modelo de rota:

* Evita a necessidade de ajustar a ordem dos pontos finais em casos comuns.
* Tentativas de corresponder às expectativas de senso comum do comportamento de roteamento.

Por exemplo, considere `/Products/List` `/Products/{id}`modelos e . Seria razoável supor `/Products/List` que é `/Products/{id}` uma combinação `/Products/List`melhor do que para o caminho da URL . Os trabalhos porque `/List` o segmento literal é considerado com `/{id}`melhor precedência do que o segmento de parâmetros .

Os detalhes de como a precedência funciona são acoplados à forma como os modelos de rota são definidos:

* Modelos com mais segmentos são considerados mais específicos.
* Um segmento com texto literal é considerado mais específico do que um segmento de parâmetros.
* Um segmento de parâmetros com restrição é considerado mais específico do que um sem.
* Um segmento complexo é considerado específico como um segmento de parâmetros com restrição.
* Pegar todos os parâmetros são os menos específicos.

Consulte o [código-fonte no GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) para obter uma referência de valores exatos.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Conceitos de geração de URL

Geração de URL:

* É o processo pelo qual o roteamento pode criar um caminho de URL baseado em um conjunto de valores de rota.
* Permite uma separação lógica entre os pontos finais e os URLs que os acessam.

O roteamento de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ponto final inclui a API. `LinkGenerator`é um serviço singleton disponível na [DI](xref:fundamentals/dependency-injection). A `LinkGenerator` API pode ser usada fora do contexto de uma solicitação de execução. [Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) e cenários <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>que dependem , como [Tag Helpers,](xref:mvc/views/tag-helpers/intro)AJUDADORES `LinkGenerator` HTML e Resultados de [Ação,](xref:mvc/controllers/actions)usam a API internamente para fornecer recursos de geração de links.

O gerador de link é respaldado pelo conceito de um **endereço** e **esquemas de endereço**. Um esquema de endereço é uma maneira de determinar os pontos de extremidade que devem ser considerados para a geração de link. Por exemplo, os cenários de valores de nome e rota de rota que muitos usuários estão familiarizados com os controladores e páginas de barbear são implementados como um esquema de endereço.

O gerador de links pode ser ligado a controladores e páginas de barbear através dos seguintes métodos de extensão:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Sobrecargas desses métodos aceitam argumentos que incluem o `HttpContext`. Esses métodos são funcionalmente equivalentes ao [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) e [url.Page,](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)mas oferecem flexibilidade e opções adicionais.

Os `GetPath*` métodos são `Url.Action` mais `Url.Page`semelhantes e, na forma, geram um URI contendo um caminho absoluto. Os métodos `GetUri*` sempre geram um URI absoluto que contém um esquema e um host. Os métodos que aceitam um `HttpContext` geram um URI no contexto da solicitação em execução. Os valores da rota [ambiente,](#ambient) o caminho base da URL, o esquema e o host da solicitação de execução são usados, a menos que seja substituído.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> é chamado com um endereço. A geração de um URI ocorre em duas etapas:

1. Um endereço é associado a uma lista de pontos de extremidade que correspondem ao endereço.
1. O <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> de cada ponto de extremidade é avaliado até que seja encontrado um padrão de rota correspondente aos valores fornecidos. A saída resultante é combinada com as outras partes de URI fornecidas ao gerador de link e é retornada.

Os métodos fornecidos pelo <xref:Microsoft.AspNetCore.Routing.LinkGenerator> dão suporte a funcionalidades de geração de link padrão para qualquer tipo de endereço. A maneira mais conveniente de usar o gerador de links é através de métodos de extensão que executam operações para um tipo de endereço específico:

| Método de extensão | Descrição |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Gera um URI com um caminho absoluto com base nos valores fornecidos. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Gera um URI absoluto com base nos valores fornecidos.             |

> [!WARNING]
> Preste atenção às seguintes implicações da chamada de métodos <xref:Microsoft.AspNetCore.Routing.LinkGenerator>:
>
> * Use métodos de extensão de `GetUri*` com cuidado em uma configuração de aplicativo que não valide o cabeçalho `Host` das solicitações de entrada. Se `Host` o cabeçalho das solicitações recebidas não for validado, a entrada de solicitação não confiável pode ser enviada de volta ao cliente em URIs em uma exibição ou página. Recomendamos que todos os aplicativos de produção configurem seu servidor para validar o cabeçalho `Host` com os valores válidos conhecidos.
>
> * Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> com cuidado no middleware em combinação com `Map` ou `MapWhen`. `Map*` altera o caminho base da solicitação em execução, o que afeta a saída da geração de link. Todas as APIs de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permitem a especificação de um caminho base. Especifique um caminho `Map*` base vazio para desfazer o efeito na geração de links.

### <a name="middleware-example"></a>Exemplo de middleware

No exemplo a seguir, um <xref:Microsoft.AspNetCore.Routing.LinkGenerator> middleware usa a API para criar um link para um método de ação que lista produtos de armazenamento. Usar o gerador de link injetando-o em uma classe e chamando `GenerateLink` está disponível para qualquer classe em um aplicativo:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Referência de modelo de rota

Os tokens dentro `{}` definem parâmetros de rota vinculados se a rota for combinada. Mais de um parâmetro de rota pode ser definido em um segmento de rota, mas os parâmetros de rota devem ser separados por um valor literal. Por exemplo, `{controller=Home}{action=Index}` não é uma rota válida, já que não há nenhum valor literal entre `{controller}` e `{action}`.  Os parâmetros de rota devem ter um nome e podem ter atributos adicionais especificados.

Um texto literal diferente dos parâmetros de rota (por exemplo, `{id}`) e do separador de caminho `/` precisa corresponder ao texto na URL. A correspondência de texto não diferencia maiúsculas de minúsculas e se baseia na representação decodificada do caminho de URLs. Para combinar com um parâmetro `{` de `}`rota literal delimitador ou , escape do delimitador repetindo o caractere. Por `{{` exemplo, ou `}}`.

Asterisco `*` ou duplo `**`asterisco:

* Pode ser usado como um prefixo para um parâmetro de rota para se ligar ao resto do URI.
* São chamados de parâmetros **de captura.** Por exemplo `blog/{**slug}`:
  * Corresponde a qualquer `/blog` URI que comece e tenha qualquer valor a segui-lo.
  * O valor `/blog` a seguir é atribuído ao valor da rota [de lesma.](https://developer.mozilla.org/docs/Glossary/Slug)

Os parâmetros catch-all também podem corresponder à cadeia de caracteres vazia.

O parâmetro catch-all escapa dos caracteres apropriados quando a rota é `/` usada para gerar uma URL, incluindo caracteres separadores de caminho. Por exemplo, a rota `foo/{*path}` com valores de rota `{ path = "my/path" }` gera `foo/my%2Fpath`. Observe o escape da barra invertida. Para fazer a viagem de ida e volta dos caracteres separadores de caminho, use o prefixo do parâmetro da rota `**`. A rota `foo/{**path}` com `{ path = "my/path" }` gera `foo/my/path`.

Padrões de URL que tentam capturar um nome de arquivo com uma extensão de arquivo opcional apresentam considerações adicionais. Por exemplo, considere o modelo `files/{filename}.{ext?}`. Quando existem valores para `filename` e `ext`, ambos os valores são populados. Se apenas um `filename` valor para existir na URL, `.` a rota corresponde porque a trilha é opcional. As URLs a seguir correspondem a essa rota:

* `/files/myFile.txt`
* `/files/myFile`

Os parâmetros de rota podem ter **valores padrão**, designados pela especificação do valor padrão após o nome do parâmetro separado por um sinal de igual (`=`). Por exemplo, `{controller=Home}` define `Home` como o valor padrão de `controller`. O valor padrão é usado se nenhum valor está presente na URL para o parâmetro. Os parâmetros de rota são opcionais anexando um ponto de interrogação (`?`) ao final do nome do parâmetro. Por exemplo, `id?`. A diferença entre valores opcionais e parâmetros de rota padrão é:

* Um parâmetro de rota com um valor padrão sempre produz um valor.
* Um parâmetro opcional tem um valor apenas quando um valor é fornecido pela URL de solicitação.

Os parâmetros de rota podem ter restrições que precisam corresponder ao valor de rota associado da URL. A `:` adição e o nome de restrição após o nome do parâmetro de rota especifica monsofam uma restrição inline em um parâmetro de rota. Se a restrição exigir argumentos, eles ficarão entre parênteses `(...)` após o nome da restrição. Várias *restrições inline* podem ser `:` especificadas anexando outro nome de restrição.

O nome da restrição e os argumentos são passados para o serviço <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> para criar uma instância de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a ser usada no processamento de URL. Por exemplo, o modelo de rota `blog/{article:minlength(10)}` especifica uma restrição `minlength` com o argumento `10`. Para obter mais informações sobre as restrições de rota e uma lista das restrições fornecidas pela estrutura, confira a seção [Referência de restrição de rota](#route-constraint-reference).

Os parâmetros de rota também podem ter transformadores de parâmetros. Os transformadores de parâmetro transformam o valor de um parâmetro ao gerar links e ações e páginas correspondentes a URLs. Como as restrições, os transformadores de parâmetros podem ser `:` adicionados em linha a um parâmetro de rota adicionando um nome e transformador após o nome do parâmetro de rota. Por exemplo, o modelo de rota `blog/{article:slugify}` especifica um transformador `slugify`. Para obter mais informações sobre transformadores de parâmetro, confira a seção [Referência de transformador de parâmetro](#parameter-transformer-reference).

A tabela a seguir demonstra modelos de rota de exemplo e seu comportamento:

| Modelo de rota                           | URI de correspondência de exemplo    | O URI de solicitação&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Somente corresponde ao caminho único `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Faz a correspondência e define `Page` como `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Faz a correspondência e define `Page` como `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | É mapeado para o controlador `Products` e a ação `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapas para `Products` o `Details` controlador`id` e ação com conjunto para 123. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapas para `Home` o `Index` controlador e método. `id` é ignorado.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Mapas para `Products` o `Index` controlador e método. `id` é ignorado.        |

Em geral, o uso de um modelo é a abordagem mais simples para o roteamento. Restrições e padrões também podem ser especificados fora do modelo de rota.

### <a name="complex-segments"></a>Segmentos complexos

Segmentos complexos são processados combinando delimitadores literais da direita para a esquerda de forma [não gananciosa.](#greedy) Por exemplo, `[Route("/a{b}c{d}")]` é um segmento complexo.
Segmentos complexos funcionam de uma maneira particular que deve ser entendida para usá-los com sucesso. O exemplo nesta seção demonstra por que segmentos complexos só funcionam bem quando o texto delimitador não aparece dentro dos valores dos parâmetros. Usar um [regex](/dotnet/standard/base-types/regular-expressions) e, em seguida, extrair manualmente os valores é necessário para casos mais complexos.

Este é um resumo das etapas que o `/a{b}c{d}` roteamento `/abcd`executa com o modelo e o caminho url . O `|` é usado para ajudar a visualizar como o algoritmo funciona:

* O primeiro literal, da `c`direita para a esquerda, é. Assim `/abcd` é pesquisado pela `/ab|c|d`direita e encontra.
* Tudo à direita`d`( ) agora é compatível `{d}`com o parâmetro de rota .
* O próximo literal, da `a`direita para a esquerda, é. Então `/ab|c|d` é pesquisado começando de onde `a` paramos, então é encontrado. `/|a|b|c|d`
* O valor à`b`direita ( ) agora é `{b}`compatível com o parâmetro de rota .
* Não há texto restante e nenhum modelo de rota restante, então este é um jogo.

Aqui está um exemplo de um caso `/a{b}c{d}` negativo usando `/aabcd`o mesmo modelo e o caminho da URL . O `|` é usado para ajudar a visualizar como o algoritmo funciona. Este caso não é compatível, o que é explicado pelo mesmo algoritmo:
* O primeiro literal, da `c`direita para a esquerda, é. Assim `/aabcd` é pesquisado pela `/aab|c|d`direita e encontra.
* Tudo à direita`d`( ) agora é compatível `{d}`com o parâmetro de rota .
* O próximo literal, da `a`direita para a esquerda, é. Então `/aab|c|d` é pesquisado começando de onde `a` paramos, então é encontrado. `/a|a|b|c|d`
* O valor à`b`direita ( ) agora é `{b}`compatível com o parâmetro de rota .
* Neste ponto há texto `a`restante, mas o algoritmo ficou sem modelo de rota para analisar, então isso não é compatível.

Uma vez que o algoritmo de correspondência não é [ganancioso:](#greedy)

* Corresponde à menor quantidade de texto possível em cada etapa.
* Qualquer caso em que o valor de limitador aparece dentro dos valores do parâmetro resulta em não correspondência.

Expressões regulares fornecem muito mais controle sobre seu comportamento correspondente.

<a name="greedy"></a>

Correspondência gananciosa, também conhecida como [partida preguiçosa,](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)combina com a maior seqüência possível. Não-ganancioso combina com a menor seqüência possível.

## <a name="route-constraint-reference"></a>Referência de restrição de rota

As restrições de rota são executadas quando ocorre uma correspondência com a URL de entrada e é criado um token do caminho da URL em valores de rota. As restrições de rota geralmente inspecionam o valor da rota associado através do modelo de rota e tomam uma decisão verdadeira ou falsa sobre se o valor é aceitável. Algumas restrições da rota usam dados fora do valor de rota para considerar se a solicitação pode ser encaminhada. Por exemplo, a <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> pode aceitar ou rejeitar uma solicitação de acordo com o verbo HTTP. As restrições são usadas em solicitações de roteamento e na geração de link.

> [!WARNING]
> Não use restrições para a validação de entrada. Se as restrições forem usadas para validação `404` de entrada, a entrada inválida resultará em uma resposta não encontrada. A entrada inválida `400` deve produzir uma solicitação ruim com uma mensagem de erro apropriada. As restrições de rota são usadas para desfazer a ambiguidade entre rotas semelhantes, não para validar as entradas de uma rota específica.

A tabela a seguir demonstra as restrições de rota de exemplo e seu comportamento esperado:

| restrição | Exemplo | Correspondências de exemplo | Observações |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Corresponde a qualquer inteiro |
| `bool` | `{active:bool}` | `true`, `FALSE` | `true` Fósforos `false`ou . Não diferenciam maiúsculas de minúsculas |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Corresponde a `DateTime` um valor válido na cultura invariante. Veja o aviso anterior. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Corresponde a `decimal` um valor válido na cultura invariante. Veja o aviso anterior.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Corresponde a `double` um valor válido na cultura invariante. Veja o aviso anterior.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Corresponde a `float` um valor válido na cultura invariante. Veja o aviso anterior.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Corresponde a um valor `Guid` válido |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Corresponde a um valor `long` válido |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | A cadeia de caracteres deve ter, no mínimo, 4 caracteres |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | A cadeia de caracteres não pode ser maior que 8 caracteres |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | A cadeia de caracteres deve ter exatamente 12 caracteres |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | A cadeia de caracteres deve ter, pelo menos, 8 e não mais de 16 caracteres |
| `min(value)` | `{age:min(18)}` | `19` | O valor inteiro deve ser, pelo menos, 18 |
| `max(value)` | `{age:max(120)}` | `91` | O valor inteiro não deve ser maior que 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | O valor inteiro deve ser, pelo menos, 18, mas não maior que 120 |
| `alpha` | `{name:alpha}` | `Rick` | String deve consistir de um `a` - `z` ou mais caracteres alfabéticos, e caso-insensível. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | A corda deve corresponder à expressão regular. Veja dicas sobre como definir uma expressão regular. |
| `required` | `{name:required}` | `Rick` | Usado para impor que um valor não parâmetro está presente durante a geração de URL |

[!INCLUDE[](~/includes/regex.md)]

Restrições múltiplas e delimitadas de cólon podem ser aplicadas a um único parâmetro. Por exemplo, a restrição a seguir restringe um parâmetro para um valor inteiro de 1 ou maior:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> As restrições de rota que verificam a URL e são convertidas para um tipo CLR sempre usam a cultura invariante. Por exemplo, conversão para `int` o `DateTime`tipo CLR ou . Essas restrições assumem que a URL não é localizável. As restrições de rota fornecidas pela estrutura não modificam os valores armazenados nos valores de rota. Todos os valores de rota analisados com base na URL são armazenados como cadeias de caracteres. Por exemplo, a restrição `float` tenta converter o valor de rota em um float, mas o valor convertido é usado somente para verificar se ele pode ser convertido em um float.

### <a name="regular-expressions-in-constraints"></a>Expressões regulares em restrições

[!INCLUDE[](~/includes/regex.md)]

Expressões regulares podem ser especificadas como `regex(...)` restrições inline usando a restrição de rota. Os métodos <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> na família também aceitam um objeto literal de restrições. Se essa forma for usada, os valores das cordas são interpretados como expressões regulares.

O código a seguir usa uma restrição reex inline:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

O código a seguir usa um objeto literal para especificar uma restrição regex:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

A estrutura do ASP.NET Core adiciona `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` ao construtor de expressão regular. Confira <xref:System.Text.RegularExpressions.RegexOptions> para obter uma descrição desses membros.

Expressões regulares usam delimitadores e tokens semelhantes aos usados pelo roteamento e pela linguagem C#. Os tokens de expressão regular precisam ter escape. Para usar a `^\d{3}-\d{2}-\d{4}$` expressão regular em uma restrição inline, use um dos seguintes:

* Substitua `\` os caracteres `\\` fornecidos na seqüência como caracteres no arquivo de origem C# para escapar do caractere de fuga de `\` seqüência.
* [Literals de cordas verbatim](/dotnet/csharp/language-reference/keywords/string).

Para escapar `{`dos caracteres delimitadores do parâmetro de roteamento, `{{` `}}` `[[` `]]` `}` `[` `]`o dobro dos caracteres na expressão, por exemplo, mas, . A tabela a seguir mostra uma expressão regular e sua versão fugitiva:

| Expressão regular    | Escapou da expressão regular     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Expressões regulares usadas no roteamento geralmente começam com o `^` caractere e correspondem à posição inicial da string. As expressões geralmente `$` terminam com o personagem e combinam com o final da seqüência. Os `^` `$` caracteres garantem que a expressão regular corresponda a todo o valor do parâmetro de rota. Sem `^` `$` os caracteres, a expressão regular corresponde a qualquer substring dentro da seqüência, o que muitas vezes é indesejável. A tabela a seguir fornece exemplos e explica por que eles correspondem ou não correspondem:

| Expression   | String    | Corresponder a | Comentário               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Sim   | A subcadeia de caracteres corresponde     |
| `[a-z]{2}`   | 123abc456 | Sim   | A subcadeia de caracteres corresponde     |
| `[a-z]{2}`   | mz        | Sim   | Corresponde à expressão    |
| `[a-z]{2}`   | MZ        | Sim   | Não diferencia maiúsculas de minúsculas    |
| `^[a-z]{2}$` | hello     | Não    | Confira `^` e `$` acima |
| `^[a-z]{2}$` | 123abc456 | Não    | Confira `^` e `$` acima |

Para saber mais sobre a sintaxe de expressões regulares, confira [Expressões regulares do .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Para restringir um parâmetro a um conjunto conhecido de valores possíveis, use uma expressão regular. Por exemplo, `{action:regex(^(list|get|create)$)}` apenas corresponde o valor da rota `action` a `list`, `get` ou `create`. Se passada para o dicionário de restrições, a cadeia de caracteres `^(list|get|create)$` é equivalente. Restrições que são passadas no dicionário de restrições que não correspondem a uma das restrições conhecidas também são tratadas como expressões regulares. As restrições que são passadas dentro de um modelo que não correspondem a uma das restrições conhecidas não são tratadas como expressões regulares.

### <a name="custom-route-constraints"></a>Restrições de rota personalizadas

As restrições de rota personalizadas <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> podem ser criadas implementando a interface. A `IRouteConstraint` interface <xref:System.Web.Routing.IRouteConstraint.Match*>contém `true` , que retorna `false` se a restrição estiver satisfeita e de outra forma.

Restrições de rota personalizadas raramente são necessárias. Antes de implementar uma restrição de rota personalizada, considere alternativas, como a vinculação do modelo.

Para usar `IRouteConstraint`um personalizado, o tipo de restrição de <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> rota deve ser registrado com o aplicativo no contêiner de serviço. O `ConstraintMap` é um dicionário que mapeia as chaves de restrição de rota para implementações de `IRouteConstraint` que validam essas restrições. É possível atualizar o `ConstraintMap` do aplicativo no `Startup.ConfigureServices` como parte de uma chamada [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou configurando <xref:Microsoft.AspNetCore.Routing.RouteOptions> diretamente com `services.Configure<RouteOptions>`. Por exemplo:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

A restrição anterior é aplicada no seguinte código:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

A implementação `MyCustomConstraint` `0` de impede que sejam aplicadas a um parâmetro de rota:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

O código anterior:

* Impede `0` no `{id}` segmento da rota.
* É mostrado para fornecer um exemplo básico de implementação de uma restrição personalizada. Ele não deve ser usado em um aplicativo de produção.

O código a seguir é uma `id` abordagem `0` melhor para evitar que um que contenha um seja processado:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

O código anterior tem as `MyCustomConstraint` seguintes vantagens sobre a abordagem:

* Não requer uma restrição personalizada.
* Ele retorna um erro mais descritivo quando `0`o parâmetro de rota inclui .

## <a name="parameter-transformer-reference"></a>Referência de parâmetro de transformador

Transformadores de parâmetro:

* Execute ao gerar um <xref:Microsoft.AspNetCore.Routing.LinkGenerator>link usando .
* Implementar <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.
* São configurados usando <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Usam o valor de rota do parâmetro e o transformam em um novo valor de cadeia de caracteres.
* Resultam no uso do valor transformado no link gerado.

Por exemplo, um transformador de parâmetro `slugify` personalizado em padrão de rota `blog\{article:slugify}` com `Url.Action(new { article = "MyTestArticle" })` gera `blog\my-test-article`.

Considere a `IOutboundParameterTransformer` seguinte implementação:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Para usar um transformador de parâmetro em um <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> `Startup.ConfigureServices`padrão de rota, configure-o usando em :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

A estrutura ASP.NET Core usa transformadores de parâmetros para transformar o URI onde um ponto final se resolve. Por exemplo, os transformadores de parâmetros `area`transformam `controller` `action`os `page`valores de rota usados para corresponder a um , e .

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Com o modelo de `SubscriptionManagementController.GetAll` rota anterior, a `/subscription-management/get-all`ação é combinada com o URI . Um transformador de parâmetro não altera os valores de rota usados para gerar um link. Por exemplo, `Url.Action("GetAll", "SubscriptionManagement")` gera `/subscription-management/get-all`.

ASP.NET Core fornece convenções de API para o uso de transformadores de parâmetros com rotas geradas:

* A <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> convenção MVC aplica um transformador de parâmetro especificado a todas as rotas de atributos no aplicativo. O transformador de parâmetro transforma os tokens de rota do atributo conforme elas são substituídas. Para obter mais informações, confira [Usar um transformador de parâmetro para personalizar a substituição de token](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> usa a convenção da API. Essa convenção aplica um transformador de parâmetro especificado a todas as Razor Pages descobertas automaticamente. O transformador de parâmetro transforma os segmentos de nome de arquivo e pasta de rotas do Razor Pages. Para obter mais informações, confira [Usar um transformador de parâmetros para personalizar rotas de página](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Referência de geração de URL

Esta seção contém uma referência para o algoritmo implementado pela geração de URL. Na prática, os exemplos mais complexos de geração de URL usam controladores ou páginas de barbear. Consulte [o roteamento nos controladores](xref:mvc/controllers/routing) para obter informações adicionais.

O processo de geração de URL começa com uma chamada para [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) ou um método semelhante. O método é fornecido com um endereço, um conjunto de valores `HttpContext`de rota e, opcionalmente, informações sobre a solicitação atual de .

O primeiro passo é usar o endereço para resolver [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) um conjunto de pontos finais do candidato usando um que corresponda ao tipo do endereço.

Uma vez que o conjunto de candidatos é encontrado pelo esquema de endereços, os pontos finais são ordenados e processados iterativamente até que uma operação de geração de URL seja bem sucedida. A geração de URL **não** verifica as ambiguidades, o primeiro resultado retornado é o resultado final.

### <a name="troubleshooting-url-generation-with-logging"></a>Solucionando a geração de URL com o registro

O primeiro passo para solucionar `Microsoft.AspNetCore.Routing` problemas na geração de URL é definir o nível de registro de . `TRACE` `LinkGenerator`registra muitos detalhes sobre seu processamento que podem ser úteis para solucionar problemas.

Consulte [a referência de geração de URL](#ugr) para obter detalhes sobre a geração de URL.

### <a name="addresses"></a>Endereços

Os endereços são o conceito na geração de URL usado para vincular uma chamada ao gerador de links a um conjunto de pontos finais do candidato.

Os endereços são um conceito extensível que vêm com duas implementações por padrão:

* Usando o nome`string`do ponto *final* () como endereço:
    * Fornece funcionalidade semelhante ao nome da rota do MVC.
    * Usa <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> o tipo de metadados.
    * Resolve a seqüência fornecida contra os metadados de todos os pontos finais registrados.
    * Abre uma exceção na inicialização se vários pontos finais usarem o mesmo nome.
    * Recomendado para uso geral fora dos controladores e páginas de barbear.
* Usando *valores de rota* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) como endereço:
    * Fornece funcionalidade semelhante aos controladores e à geração de URL legado razor pages.
    * Muito complexo para estender e depurar.
    * Fornece a implementação usada por `IUrlHelper`, Tag Helpers, HTML Helpers, Action Results, etc.

O papel do esquema de endereço é fazer a associação entre o endereço e os pontos finais correspondentes por critérios arbitrários:

* O esquema de nome de ponto final executa uma pesquisa básica do dicionário.
* O esquema de valores de rota tem um complexo melhor subconjunto de algoritmo de conjunto.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Valores ambientais e valores explícitos

A partir da solicitação atual, o roteamento `HttpContext.Request.RouteValues`acessa os valores de rota da solicitação atual. Os valores associados à solicitação atual são chamados de **valores ambientais.** Para fins de clareza, a documentação refere-se aos valores de rota repassados aos métodos como **valores explícitos.**

O exemplo a seguir mostra valores ambientais e valores explícitos. Fornece valores ambientais a partir da `{ id = 17, }`solicitação atual e valores explícitos: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

O código anterior:

* Retorna `/Widget/Index/17`
* Fica <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [DI](xref:fundamentals/dependency-injection).

O código a seguir não fornece `{ controller = "Home", action = "Subscribe", id = 17, }`valores ambientais e valores explícitos: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

O método anterior retorna`/Home/Subscribe/17`

O seguinte código `WidgetController` `/Widget/Subscribe/17`nas devoluções:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

O código a seguir fornece ao controlador a partir `{ action = "Edit", id = 17, }`de valores ambientais na solicitação atual e valores explícitos: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

No código anterior:

* `/Gadget/Edit/17`é devolvido.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>recebe <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>o .
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
gera uma URL com um caminho absoluto para um método de ação. A URL contém `action` o `route` nome e os valores especificados.

O código a seguir fornece valores ambientais `{ page = "./Edit, id = 17, }`a partir da solicitação atual e valores explícitos: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

O código `url` anterior `/Edit/17` define-se para quando a Página de navalha de edição contém a seguinte diretiva de página:

 `@page "{id:int}"`

Se a página Editar não `"{id:int}"` contiver `url` `/Edit?id=17`o modelo de rota, será .

O comportamento dos MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> adiciona uma camada de complexidade além das regras descritas aqui:

* `IUrlHelper`sempre fornece os valores de rota da solicitação atual como valores ambientais.
* [O IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) sempre `action` `controller` copia os valores atuais e de rota como valores explícitos, a menos que substituído pelo desenvolvedor.
* [IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) sempre copia `page` o valor de rota atual como um valor explícito, a menos que substituído. <!--by the user-->
* `IUrlHelper.Page`sempre substitui o `handler` valor `null` de rota atual com como valores explícitos, a menos que substituído.

Os usuários são muitas vezes surpreendidos pelos detalhes comportamentais dos valores ambientais, porque o MVC parece não seguir suas próprias regras. Por razões históricas e de `action`compatibilidade, certos valores de rota, tais como , `controller`, `page`e `handler` têm seu próprio comportamento de caso especial.

A funcionalidade equivalente fornecida `LinkGenerator.GetPathByAction` `LinkGenerator.GetPathByPage` e duplica essas anomalias de `IUrlHelper` compatibilidade.

### <a name="url-generation-process"></a>Processo de geração de URL

Uma vez que o conjunto de pontos finais do candidato é encontrado, o algoritmo de geração de URL:

* Processa os pontos finais de forma iterativa.
* Retorna o primeiro resultado bem sucedido.

A primeira etapa deste processo é chamada **de invalidação de valor de rota**.  A invalidação do valor da rota é o processo pelo qual o roteamento decide quais valores de rota dos valores ambientais devem ser usados e quais devem ser ignorados. Cada valor ambiente é considerado e combinado com os valores explícitos, ou ignorado.

A melhor maneira de pensar sobre o papel dos valores ambientais é que eles tentam salvar os desenvolvedores de aplicativos digitando, em alguns casos comuns. Tradicionalmente, os cenários em que os valores ambientais são úteis estão relacionados ao MVC:

* Ao vincular-se a outra ação no mesmo controlador, o nome do controlador não precisa ser especificado.
* Ao vincular a outro controlador na mesma área, o nome da área não precisa ser especificado.
* Ao vincular-se ao mesmo método de ação, os valores de rota não precisam ser especificados.
* Ao vincular a outra parte do aplicativo, você não quer carregar valores de rota que não têm significado nessa parte do aplicativo.

Chamadas `LinkGenerator` para `IUrlHelper` ou `null` esse retorno geralmente são causadas por não entender a invalidação do valor da rota. Solucionar problemas na invalidação do valor da rota especificando explicitamente mais valores de rota para ver se isso resolve o problema.

A invalidação de valor de rota funciona com o pressuposto de que o esquema de URL do aplicativo é hierárquico, com uma hierarquia formada da esquerda para a direita. Considere o modelo `{controller}/{action}/{id?}` básico de rota do controlador para ter uma noção intuitiva de como isso funciona na prática. Uma **alteração** para um valor **invalida** todos os valores de rota que aparecem à direita. Isso reflete a suposição sobre hierarquia. Se o aplicativo tiver `id`um valor ambiente para , e `controller`a operação especificar um valor diferente para:

* `id`não será reutilizado `{controller}` porque está à `{id?}`esquerda de .

Alguns exemplos demonstram esse princípio:

* Se os valores explícitos contiverem um valor para `id`, o valor ambiente para `id` é ignorado. Os valores `controller` `action` ambientais para e podem ser usados.
* Se os valores explícitos contiverem um valor para `action`, qualquer valor ambiente para `action` é ignorado. Os valores `controller` ambientais podem ser usados. Se o valor `action` explícito for for `action`different `id` do valor ambiente para , o valor não será usado.  Se o valor `action` explícito for for o `action`mesmo `id` que o valor ambiente para , o valor pode ser usado.
* Se os valores explícitos contiverem um valor para `controller`, qualquer valor ambiente para `controller` é ignorado. Se o valor `controller` explícito for for `controller`different `action` do `id` valor ambiente para , os valores e não serão usados. Se o valor `controller` explícito for é o `controller`mesmo `action` `id` do valor ambiente para , os valores podem ser usados.

Esse processo é ainda mais complicado pela existência de rotas de atributos e rotas convencionais dedicadas. Controller rotas convencionais, como `{controller}/{action}/{id?}` especificar uma hierarquia usando parâmetros de rota. Para [rotas convencionais dedicadas](xref:mvc/controllers/routing#dcr) e [rotas de atributo](xref:mvc/controllers/routing#ar) para controladores e páginas de barbear:

* Há uma hierarquia de valores de rota.
* Eles não aparecem no modelo.

Para esses casos, a geração de URL define o conceito **de valores necessários.** Os pontos finais criados pelos controladores e páginas de barbear têm valores exigidos especificados que permitem que a invalidação de valor de rota funcione.

O algoritmo de invalidação de valor de rota em detalhes:

* Os nomes de valor necessários são combinados com os parâmetros de rota e, em seguida, processados da esquerda para a direita.
* Para cada parâmetro, o valor ambiente e o valor explícito são comparados:
    * Se o valor ambiente e o valor explícito forem os mesmos, o processo continua.
    * Se o valor ambiente estiver presente e o valor explícito não estiver, o valor ambiente será usado ao gerar a URL.
    * Se o valor ambiente não estiver presente e o valor explícito estiver, rejeite o valor ambiente e todos os valores ambientais subsequentes.
    * Se o valor ambiente e o valor explícito estiverem presentes, e os dois valores forem diferentes, rejeite o valor ambiente e todos os valores ambientais subsequentes.

Neste ponto, a operação de geração de URL está pronta para avaliar as restrições de rota. O conjunto de valores aceitos é combinado com os valores padrão do parâmetro, que é fornecido às restrições. Se as restrições passarem, a operação continua.

Em seguida, os **valores aceitos** podem ser usados para expandir o modelo de rota. O modelo de rota é processado:

* Da esquerda para a direita.
* Cada parâmetro tem seu valor aceito substituído.
* Com os seguintes casos especiais:
  * Se os valores aceitos perderem um valor e o parâmetro tiver um valor padrão, o valor padrão será usado.
  * Se os valores aceitos estão faltando um valor e o parâmetro é opcional, o processamento continua.
  * Se algum parâmetro de rota à direita de um parâmetro opcional ausente tiver um valor, a operação falhará.
  * <!-- review default-valued parameters optional parameters --> Parâmetros de valor padrão contíguos e parâmetros opcionais são colapsados sempre que possível.

Valores explicitamente fornecidos que não correspondem a um segmento da rota são adicionados à seqüência de consultas. A tabela a seguir mostra o resultado do uso do modelo de rota `{controller}/{action}/{id?}`.

| Valores de ambiente                     | Valores explícitos                        | Result                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controlador = "Home"                | ação = "About"                       | `/Home/About`           |
| controlador = "Home"                | controlador = "Order", ação = "About" | `/Order/About`          |
| controlador = "Home", cor = "Red" | ação = "About"                       | `/Home/About`           |
| controlador = "Home"                | ação = "About", cor = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Problemas com a invalidação do valor da rota

A partir de ASP.NET O Core 3.0, alguns esquemas de geração de URL usados no início ASP.NET versões Core não funcionam bem com a geração de URL. A equipe do ASP.NET Core planeja adicionar recursos para atender a essas necessidades em um lançamento futuro. Por enquanto, a melhor solução é usar roteamento legado.

O código a seguir mostra um exemplo de um esquema de geração de URL que não é suportado pelo roteamento.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

No código anterior, `culture` o parâmetro de rota é usado para localização. O desejo é `culture` ter o parâmetro sempre aceito como valor ambiente. No entanto, o `culture` parâmetro não é aceito como um valor ambiente devido à forma como os valores necessários funcionam:

* No `"default"` modelo de `culture` rota, o parâmetro de `controller`rota está `controller` à esquerda de `culture`, portanto, altera para não invalidar .
* No `"blog"` modelo de `culture` rota, o parâmetro de rota `controller`é considerado à direita de , que aparece nos valores necessários.

## <a name="configuring-endpoint-metadata"></a>Configuração de metadados de ponto final

Os links a seguir fornecem informações sobre a configuração de metadados de ponto final:

* [Habilite cors com roteamento de ponto final](xref:security/cors#enable-cors-with-endpoint-routing)
* [IAuthorizationPolicyProvider amostra](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) usando `[MinimumAgeAuthorize]` um atributo personalizado
* [Autenticação de teste com o atributo [Autorizar]](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Selecionando o esquema com o atributo [Autorizar]](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Aplicando políticas usando o atributo [Autorizar]](xref:security/authorization/policies#applying-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Correspondência de host em rotas com RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>aplica uma restrição à rota que requer o host especificado. O `RequireHost` parâmetro [ou [Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) pode ser:

* Anfitrião: `www.domain.com`, `www.domain.com` combina com qualquer porta.
* Hospede com `*.domain.com`curinga: , fósforos `www.domain.com`, `subdomain.domain.com`, ou `www.subdomain.domain.com` em qualquer porta.
* Porta: `*:5000`, corresponde a porta 5000 com qualquer host.
* Host e `www.domain.com:5000` porta: ou `*.domain.com:5000`, corresponde host e porta.

Vários parâmetros podem `RequireHost` ser `[Host]`especificados usando ou . A restrição corresponde aos hosts válidos para qualquer um dos parâmetros. Por `[Host("domain.com", "*.domain.com")]` exemplo, `domain.com` `www.domain.com`fósforos `subdomain.domain.com`e.

O código `RequireHost` a seguir é necessário para exigir o host especificado na rota:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

O código a `[Host]` seguir usa o atributo no controlador para exigir qualquer um dos hosts especificados:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Quando `[Host]` o atributo é aplicado ao controlador e ao método de ação:

* O atributo na ação é usado.
* O atributo do controlador é ignorado.

## <a name="performance-guidance-for-routing"></a>Orientação de desempenho para roteamento

A maior parte do roteamento foi atualizada em ASP.NET Núcleo 3.0 para aumentar o desempenho.

Quando um aplicativo tem problemas de desempenho, muitas vezes é suspeito o roteamento como o problema. A razão pela qual o roteamento é que estruturas como controladores e páginas de barbear relatam a quantidade de tempo gasto dentro da estrutura em suas mensagens de registro. Quando há uma diferença significativa entre o tempo relatado pelos controladores e o tempo total da solicitação:

* Os desenvolvedores eliminam seu código de aplicativo como a fonte do problema.
* É comum assumir que o encaminhamento é a causa.

O roteamento é testado com desempenho usando milhares de pontos finais. É improvável que um aplicativo típico encontre um problema de desempenho apenas por ser muito grande. A causa raiz mais comum do desempenho de roteamento lento é geralmente um middleware personalizado mal comportado.

Esta amostra de código a seguir demonstra uma técnica básica para reduzir a fonte de atraso:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Para o roteamento do tempo:

* Intercale cada middleware com uma cópia do middleware de tempo mostrado no código anterior.
* Adicione um identificador exclusivo para correlacionar os dados de tempo com o código.

Esta é uma maneira básica de reduzir o atraso quando é `10ms`significativo, por exemplo, mais do que .  Subtraindo `Time 2` `Time 1` dos relatórios o `UseRouting` tempo gasto dentro do middleware.

O código a seguir usa uma abordagem mais compacta do código de tempo anterior:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Recursos de roteamento potencialmente caros

A lista a seguir fornece algumas informações sobre os recursos de roteamento que são relativamente caros em comparação com os modelos básicos de rota:

* Expressões regulares: É possível escrever expressões regulares que são complexas, ou têm tempo de execução longo com uma pequena quantidade de entrada.

* Segmentos`{x}-{y}-{z}`complexos ( ): 
  * São significativamente mais caros do que analisar um segmento de caminho de URL regular.
  * Resultando em muito mais substrings sendo alocados.
  * A lógica complexa do segmento não foi atualizada em ASP.NET atualização de desempenho de roteamento do Core 3.0.

* Acesso síncrono de dados: Muitos aplicativos complexos têm acesso ao banco de dados como parte de seu roteamento. ASP.NET Core 2.2 e o roteamento anterior podem não fornecer os pontos de extensibilidade certos para suportar o roteamento de acesso ao banco de dados. Por exemplo, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> e são síncronos. Pontos de extensibilidade como <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> e <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> são assíncronos.

## <a name="guidance-for-library-authors"></a>Orientação para autores de bibliotecas

Esta seção contém orientações para autores de bibliotecas que se baseiam em cima do roteamento. Esses detalhes visam garantir que os desenvolvedores de aplicativos tenham uma boa experiência usando bibliotecas e frameworks que estendem o roteamento.

### <a name="define-endpoints"></a>Definir pontos finais

Para criar uma estrutura que use roteamento para correspondência de URL, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>comece definindo uma experiência de usuário que se baseia em cima de .

**DO** construa <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>em cima de . Isso permite que os usuários componham sua estrutura com outros recursos ASP.NET Core sem confusão. Cada modelo ASP.NET Core inclui roteamento. Assuma que o roteamento está presente e familiar para os usuários.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**DO** devolva um tipo `MapMyFramework(...)` de concreto <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>selado de uma chamada para que implemente . A `Map...` maioria dos métodos-estrutura seguem esse padrão. A `IEndpointConventionBuilder` interface:

* Permite a composição de metadados.
* É alvo de uma variedade de métodos de extensão.

Declarar seu próprio tipo permite que você adicione sua própria funcionalidade específica da estrutura ao construtor. Não há problema em embrulhar um construtor declarado e encaminhar chamadas para ele.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**CONSIDERE** escrever <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>o seu próprio . `EndpointDataSource`é o primitivo de baixo nível para declarar e atualizar uma coleção de pontos finais. `EndpointDataSource`é uma Poderosa API usada por controladores e páginas de barbear.

Os testes de roteamento têm um [exemplo básico](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) de uma fonte de dados não atualizada.

**NÃO** tente registrar `EndpointDataSource` um por padrão. Exija que os usuários <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>registrem sua estrutura em . A filosofia do roteamento é que nada `UseEndpoints` está incluído por padrão, e esse é o lugar para registrar pontos finais.

### <a name="creating-routing-integrated-middleware"></a>Criando middleware integrado de roteamento

**CONSIDERE** definir os tipos de metadados como uma interface.

**O DO** torna possível o uso de tipos de metadados como um atributo em classes e métodos.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Frameworks como controladores e páginas de barbear suportam a aplicação de atributos de metadados a tipos e métodos. Se você declarar tipos de metadados:

* Torná-los acessíveis como [atributos.](/dotnet/csharp/programming-guide/concepts/attributes/)
* A maioria dos usuários está familiarizada com a aplicação de atributos.

Declarar um tipo de metadados como interface adiciona outra camada de flexibilidade:

* Interfaces são compostas.
* Os desenvolvedores podem declarar seus próprios tipos que combinam várias políticas.

**Do** torna possível substituir metadados, como mostrado no exemplo a seguir:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

A melhor maneira de seguir essas diretrizes é evitar a definição de **metadados de marcadores:**

* Não basta procurar a presença de um tipo de metadados.
* Defina uma propriedade nos metadados e verifique a propriedade.

A coleta de metadados é ordenada e suporta a substituição por prioridade. No caso dos controladores, os metadados sobre o método de ação são mais específicos.

**FAÇA** middleware útil com e sem roteamento.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

Como exemplo desta diretriz, considere `UseAuthorization` o middleware. O middleware de autorização permite que você passe em uma política de recuo. <!-- shown where?  (shown here) --> A política de recuo, se especificada, aplica-se a ambos:

* Endpoints sem uma política especificada.
* Pedidos que não correspondem a um ponto final.

Isso torna o middleware de autorização útil fora do contexto de roteamento. O middleware de autorização pode ser usado para programação tradicional de middleware.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

O roteamento é responsável por mapear URIs de solicitação para endpoints e despachar solicitações recebidas para esses pontos finais. As rotas são definidas no aplicativo e configuradas quando o aplicativo é iniciado. Uma rota pode opcionalmente extrair os valores da URL contida na solicitação e esses valores podem então ser usados para o processamento da solicitação. Usando informações de rota do aplicativo, o roteamento também é capaz de gerar URLs que mapeiam para pontos finais.

Para usar os últimos cenários de roteamento no ASP.NET Core 2.2, especifique a [versão de compatibilidade](xref:mvc/compatibility-version) com o registro de serviços MVC em `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

A opção <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> determina se o roteamento deve usar internamente a lógica baseada em ponto de extremidade ou a lógica baseada em <xref:Microsoft.AspNetCore.Routing.IRouter> do ASP.NET Core 2.1 ou anterior. Quando a versão de compatibilidade é definida como 2.2 ou posterior, o valor padrão é `true`. Defina o valor como `false` para usar a lógica de roteamento anterior:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Para obter mais informações sobre o roteamento baseado em <xref:Microsoft.AspNetCore.Routing.IRouter>, confira a [versão do ASP.NET Core 2.1 deste tópico](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

> [!IMPORTANT]
> Este documento aborda o roteamento de nível inferior do ASP.NET Core. Para obter informações sobre o roteamento do ASP.NET Core MVC, confira <xref:mvc/controllers/routing>. Para obter informações sobre convenções de roteamento no Razor Pages, confira <xref:razor-pages/razor-pages-conventions>.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Conceitos básicos sobre roteamento

A maioria dos aplicativos deve escolher um esquema de roteamento básico e descritivo para que as URLs sejam legíveis e significativas. A rota convencional padrão `{controller=Home}/{action=Index}/{id?}`:

* Dá suporte a um esquema de roteamento básico e descritivo.
* É um ponto de partida útil para aplicativos baseados em interface do usuário.

Os desenvolvedores geralmente adicionam rotas adicionais de terse a áreas de alto tráfego de um aplicativo em situações especializadas usando [roteamento de atributos](xref:mvc/controllers/routing#attribute-routing) ou rotas convencionais dedicadas. Exemplos de situações especializadas incluem pontos finais de blog e comércio eletrônico.

As APIs da Web devem usar o roteamento de atributo para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por verbos HTTP. Isso significa que muitas operações, por exemplo, GET e POST, no mesmo recurso lógico usam a mesma URL. O roteamento de atributo fornece um nível de controle necessário para projetar cuidadosamente o layout de ponto de extremidade público de uma API.

Os aplicativos do Razor Pages usam o roteamento convencional padrão para fornecer recursos nomeados na pasta *Pages* de um aplicativo. Estão disponíveis convenções adicionais que permitem a personalização do comportamento de roteamento do Razor Pages. Para obter mais informações, consulte <xref:razor-pages/index> e <xref:razor-pages/razor-pages-conventions>.

O suporte à geração de URL permite que o aplicativo seja desenvolvido sem hard-coding das URLs para vincular o aplicativo. Esse suporte permite começar com uma configuração de roteamento básica e modificar as rotas, depois que o layout de recurso do aplicativo é determinado.

O roteamento`Endpoint`usa pontos *finais* () para representar pontos finais lógicos em um aplicativo.

Um ponto de extremidade define um delegado para processar solicitações e uma coleção de metadados arbitrários. Os metadados usados implementam interesses paralelos com base em políticas e na configuração anexada a cada ponto de extremidade.

O sistema de roteamento tem as seguintes características:

* A sintaxe do modelo de rota é usada para definir rotas com os parâmetros de rota com tokens criados.
* A configuração de ponto de extremidade de estilo convencional e de estilo de atributo é permitida.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> é usado para determinar se um parâmetro de URL contém um valor válido para determinada restrição de ponto de extremidade.
* Modelos de aplicativo, como MVC/Razor Pages, registram todos os seus pontos de extremidade, que têm uma implementação previsível de cenários de roteamento.
* A implementação de roteamento toma decisões de roteamento, sempre que desejado no pipeline de middleware.
* O Middleware que aparece após um Middleware de Roteamento pode inspecionar o resultado da decisão de ponto de extremidade do Middleware de Roteamento para determinado URI de solicitação.
* É possível enumerar todos os pontos de extremidade no aplicativo em qualquer lugar do pipeline de middleware.
* Um aplicativo pode usar o roteamento para gerar URLs (por exemplo, para redirecionamento ou links) com base nas informações de ponto de extremidade e evitar URLs embutidas em código, o que ajuda na facilidade de manutenção.
* A geração de URL baseia-se em endereços, que dá suporte à extensibilidade arbitrária:

  * A API de Gerador de Link (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) pode ser resolvida em qualquer lugar usando a [DI (Injeção de Dependência)](xref:fundamentals/dependency-injection) para gerar URLs.
  * Quando a API de Gerador de Link não está disponível por meio da DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> oferece métodos para criar URLs.

> [!NOTE]
> Com o lançamento do roteamento de ponto de extremidade no ASP.NET Core 2.2, a vinculação de ponto de extremidade fica limitada às ações e às páginas do MVC/Razor Pages. As expansões de funcionalidades de vinculação de ponto de extremidade estão planejadas para versões futuras.

O roteamento está conectado ao pipeline do [middleware](xref:fundamentals/middleware/index) pela classe <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>. O [ASP.NET Core MVC](xref:mvc/overview) adiciona o roteamento ao pipeline de middleware como parte de sua configuração e manipula o roteamento nos aplicativos do MVC e do Razor Pages. Para saber como usar o roteamento como um componente autônomo, confira a seção [Usar o Middleware de Roteamento](#use-routing-middleware).

### <a name="url-matching"></a>Correspondência de URL

A correspondência de URL é o processo pelo qual o roteamento expede uma solicitação de entrada para um *ponto de extremidade*. Esse processo se baseia nos dados do caminho da URL, mas pode ser estendido para considerar qualquer dado na solicitação. A capacidade de expedir solicitações para manipuladores separados é fundamental para dimensionar o tamanho e a complexidade de um aplicativo.

O sistema de roteamento no roteamento de ponto de extremidade é responsável por todas as decisões de expedição. Como o middleware aplica políticas com base no ponto de extremidade selecionado, é importante que qualquer decisão que possa afetar a expedição ou a aplicação de políticas de segurança seja feita dentro do sistema de roteamento.

Quando o delegado do ponto de extremidade é executado, as propriedades de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) são definidas com valores apropriados com base no processamento da solicitação executado até o momento.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) é um dicionário de *valores de rota* produzido por meio da rota. Esses valores geralmente são determinados pela criação de token da URL e podem ser usados para aceitar a entrada do usuário ou tomar outras decisões de expedição dentro do aplicativo.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) é um recipiente de propriedades de dados adicionais relacionados à rota correspondente. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> são fornecidos para dar suporte à associação de dados de estado com cada rota para que o aplicativo possa tomar decisões com base em qual rota teve uma correspondência. Esses valores são definidos pelo desenvolvedor e **não** afetam de forma alguma o comportamento do roteamento. Além disso, os valores em stash em [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) podem ser de qualquer tipo, ao contrário de [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), que devem ser conversíveis de/para cadeias de caracteres.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) é uma lista das rotas que participaram da correspondência bem-sucedida da solicitação. As rotas podem ser aninhadas uma dentro da outra. A propriedade <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> reflete o caminho pela árvore lógica de rotas que resultou em uma correspondência. Em geral, o primeiro item em <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> é a coleção de rotas e deve ser usado para a geração de URL. O último item em <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> é o manipulador de rotas que teve uma correspondência.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Geração de URL com o LinkGenerator

Geração de URL é o processo pelo qual o roteamento pode criar um caminho de URL de acordo com um conjunto de valores de rota. Isso permite uma separação lógica entre os pontos de extremidade e as URLs que os acessam.

O roteamento de ponto de extremidade inclui a API de Gerador de Link (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>é um serviço singleton que pode ser recuperado de [DI](xref:fundamentals/dependency-injection). A API pode ser usada fora do contexto de uma solicitação em execução. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> do MVC e cenários que dependem de <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, como [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro), Auxiliares de HTML e [Resultados da Ação](xref:mvc/controllers/actions), usam o gerador de link para fornecer funcionalidades de geração de link.

O gerador de link é respaldado pelo conceito de um *endereço* e *esquemas de endereço*. Um esquema de endereço é uma maneira de determinar os pontos de extremidade que devem ser considerados para a geração de link. Por exemplo, os cenários de nome de rota e valores de rota com os quais muitos usuários estão familiarizados no MVC/Razor Pages são implementados como um esquema de endereço.

O gerador de link pode ser vinculado a ações e páginas do MVC/Razor Pages por meio dos seguintes métodos de extensão:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Uma sobrecarga desses métodos aceita argumentos que incluem o `HttpContext`. Esses métodos são funcionalmente equivalentes a `Url.Action` e `Url.Page`, mas oferecem mais flexibilidade e opções.

Os métodos `GetPath*` são mais semelhantes a `Url.Action` e `Url.Page`, pois geram um URI que contém um caminho absoluto. Os métodos `GetUri*` sempre geram um URI absoluto que contém um esquema e um host. Os métodos que aceitam um `HttpContext` geram um URI no contexto da solicitação em execução. Os valores de rota de ambiente, o caminho base da URL, o esquema e o host da solicitação em execução são usados, a menos que sejam substituídos.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> é chamado com um endereço. A geração de um URI ocorre em duas etapas:

1. Um endereço é associado a uma lista de pontos de extremidade que correspondem ao endereço.
1. O `RoutePattern` de cada ponto de extremidade é avaliado até que seja encontrado um padrão de rota correspondente aos valores fornecidos. A saída resultante é combinada com as outras partes de URI fornecidas ao gerador de link e é retornada.

Os métodos fornecidos pelo <xref:Microsoft.AspNetCore.Routing.LinkGenerator> dão suporte a funcionalidades de geração de link padrão para qualquer tipo de endereço. A maneira mais conveniente usar o gerador de link é por meio de métodos de extensão que executam operações para um tipo de endereço específico.

| Método de extensão   | Descrição                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Gera um URI com um caminho absoluto com base nos valores fornecidos. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Gera um URI absoluto com base nos valores fornecidos.             |

> [!WARNING]
> Preste atenção às seguintes implicações da chamada de métodos <xref:Microsoft.AspNetCore.Routing.LinkGenerator>:
>
> * Use métodos de extensão de `GetUri*` com cuidado em uma configuração de aplicativo que não valide o cabeçalho `Host` das solicitações de entrada. Se o cabeçalho `Host` das solicitações de entrada não é validado, uma entrada de solicitação não confiável pode ser enviada novamente ao cliente em URIs em uma exibição/página. Recomendamos que todos os aplicativos de produção configurem seu servidor para validar o cabeçalho `Host` com os valores válidos conhecidos.
>
> * Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> com cuidado no middleware em combinação com `Map` ou `MapWhen`. `Map*` altera o caminho base da solicitação em execução, o que afeta a saída da geração de link. Todas as APIs de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permitem a especificação de um caminho base. Sempre especifique um caminho base vazio para desfazer o efeito de `Map*` na geração de link.

## <a name="differences-from-earlier-versions-of-routing"></a>Diferenças das versões anteriores de roteamento

Existem algumas diferenças entre o roteamento de ponto de extremidade no ASP.NET Core 2.2 ou posterior e nas versões anteriores de roteamento no ASP.NET Core:

* O sistema de roteamento do ponto de extremidade não dá suporte à extensibilidade baseada em <xref:Microsoft.AspNetCore.Routing.IRouter>, incluindo a herança de <xref:Microsoft.AspNetCore.Routing.Route>.

* O roteamento de ponto de extremidade não dá suporte a [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Use a [versão de](xref:mvc/compatibility-version) compatibilidade`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`2.1 ( ) para continuar usando o shim de compatibilidade.

* O Roteamento de Ponto de Extremidade tem um comportamento diferente para o uso de maiúsculas dos URIs gerados ao usar rotas convencionais.

  Considere o seguinte modelo de rota padrão:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Suponha que você gere um link para uma ação usando a seguinte rota:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Com o roteamento baseado em <xref:Microsoft.AspNetCore.Routing.IRouter>, esse código gera um URI igual a `/blog/ReadPost/17`, que respeita o uso de maiúsculas do valor de rota fornecido. O roteamento de ponto de extremidade no ASP.NET Core 2.2 ou posterior produz `/Blog/ReadPost/17` ("Blog" está em letras maiúsculas). O roteamento de ponto de extremidade fornece a interface `IOutboundParameterTransformer` que pode ser usada para personalizar esse comportamento globalmente ou para aplicar diferentes convenções ao mapeamento de URLs.

  Para obter mais informações, confira a seção [Referência de transformador de parâmetro](#parameter-transformer-reference).

* A Geração de Link usada pelo MVC/Razor Pages com rotas convencionais tem um comportamento diferente ao tentar estabelecer um vínculo com um controlador/uma ação ou uma página não existente.

  Considere o seguinte modelo de rota padrão:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Suponha que você gere um link para uma ação usando o modelo padrão com o seguinte:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Com o roteamento baseado em `IRouter`, o resultado é sempre `/Blog/ReadPost/17`, mesmo se o `BlogController` não existe ou não tem um método de ação `ReadPost`. Conforme esperado, o roteamento de ponto de extremidade no ASP.NET Core 2.2 ou posterior produz `/Blog/ReadPost/17` se o método de ação existe. *No entanto, o roteamento de ponto de extremidade produz uma cadeia de caracteres vazia se a ação não existe.* Conceitualmente, o roteamento de ponto de extremidade não pressupõe a existência do ponto de extremidade, caso a ação não exista.

* O *algoritmo de invalidação de valor de ambiente* da geração de link tem um comportamento diferente quando usado com o roteamento de ponto de extremidade.

  A *invalidação de valor de ambiente* é o algoritmo que decide quais valores de rota da solicitação em execução no momento (os valores de ambiente) podem ser usados em operações de geração de link. O roteamento convencional sempre invalidava valores de rota extras ao estabelecer o vínculo com uma ação diferente. O roteamento de atributo não tinha esse comportamento antes do lançamento do ASP.NET Core 2.2. Em versões anteriores do ASP.NET Core, os links para outra ação que usam os mesmos nomes de parâmetro de rota resultavam em erros de geração de link. No ASP.NET Core 2.2 ou posterior, as duas formas de roteamento invalidam os valores ao estabelecer o vínculo com outra ação.

  Considere o exemplo a seguir no ASP.NET Core 2.1 ou anterior. Ao estabelecer o vínculo com outra ação (ou outra página), os valores de rota podem ser reutilizados de maneiras indesejadas.

  Em */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  Em */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Se o URI é `/Store/Product/18` no ASP.NET Core 2.1 ou anterior, o link gerado na página Store/Info por `@Url.Page("/Login")` é `/Login/18`. O valor de `id` igual a 18 é reutilizado, mesmo que o destino do link seja uma parte totalmente diferente do aplicativo. O valor de rota de `id` no contexto da página `/Login` provavelmente é um valor de ID de usuário, e não um valor de ID do produto (product ID) da loja.

  No roteamento de ponto de extremidade com o ASP.NET Core 2.2 ou posterior, o resultado é `/Login`. Os valores de ambiente não são reutilizados quando o destino vinculado é uma ação ou uma página diferente.

* Sintaxe do parâmetro de rota de viagem de ida e volta: as barras "/" não são codificadas ao usar uma sintaxe do parâmetro catch-all de asterisco duplo (`**`).

  Durante a geração de link, o sistema de roteamento codifica o valor capturado em um parâmetro catch-all de asterisco duplo (`**`) (por exemplo, `{**myparametername}`), exceto as barras "/". O catch-all de asterisco duplo é compatível com o roteamento baseado em `IRouter` no ASP.NET Core 2.2 ou posterior.

  A sintaxe do parâmetro catch-all de asterisco único em versões anteriores do ASP.NET Core (`{*myparametername}`) permanece com suporte e as barras "/" são codificadas.

  | Rota              | Link gerado com<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (a barra "/" é codificada)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Exemplo de middleware

No exemplo a seguir, um middleware usa a API de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> para criar um link para um método de ação que lista os produtos da loja. O uso do gerador de link com sua injeção em uma classe e uma chamada a `GenerateLink` está disponível para qualquer classe em um aplicativo.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>Criar rotas

A maioria dos aplicativos cria rotas chamando <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ou um dos métodos de extensão semelhantes definidos em <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Qualquer um dos métodos de extensão de <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> cria uma instância de <xref:Microsoft.AspNetCore.Routing.Route> e a adicionam à coleção de rotas.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> não aceita um parâmetro de manipulador de rotas. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> apenas adiciona rotas que são manipuladas pelo <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Para saber mais sobre o roteamento no MVC, confira <xref:mvc/controllers/routing>.

O exemplo de código a seguir é um exemplo de uma chamada <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> usada por uma definição de rota típica do ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Esse modelo corresponde a um caminho de URL e extrai os valores de rota. Por exemplo, o caminho `/Products/Details/17` gera os seguintes valores de rota: `{ controller = Products, action = Details, id = 17 }`.

Os valores de rota são determinados pela divisão do caminho da URL em segmentos e pela correspondência de cada segmento com o nome do *parâmetro de rota* no modelo de rota. Os parâmetros de rota são nomeados. Os parâmetros são definidos com a colocação do nome do parâmetro em chaves `{ ... }`.

O modelo anterior também pode corresponder ao caminho da URL `/` e produzir os valores `{ controller = Home, action = Index }`. Isso ocorre porque os parâmetros de rota `{controller}` e `{action}` têm valores padrão e o parâmetro de rota `id` é opcional. Um sinal de igual (`=`) seguido de um valor após o nome do parâmetro de rota define um valor padrão para o parâmetro. Um ponto de interrogação (`?`) após o nome do parâmetro de rota define o parâmetro como opcional.

Os parâmetros de rota com um valor padrão *sempre* produzem um valor de rota quando a rota corresponde. Os parâmetros opcionais não produzem um valor de rota se não houver um segmento de caminho de URL correspondente. Confira a seção [Referência de modelo de rota](#route-template-reference) para obter uma descrição completa dos recursos e da sintaxe de modelo de rota.

No seguinte exemplo, a definição do parâmetro de rota `{id:int}` define uma [restrição de rota](#route-constraint-reference) para o parâmetro de rota `id`:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Esse modelo corresponde a um caminho de URL, como `/Products/Details/17`, mas não como `/Products/Details/Apples`. As restrições de rota implementam <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> e inspecionam valores de rota para verificá-los. Neste exemplo, o valor de rota `id` precisa ser conversível em um inteiro. Confira [route-constraint-reference](#route-constraint-reference) para obter uma explicação das restrições de rota fornecidas pela estrutura.

Sobrecargas adicionais de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> aceitam valores para `constraints`, `dataTokens` e `defaults`. O uso típico desses parâmetros é passar um objeto de tipo anônimo, no qual os nomes da propriedade do tipo anônimo correspondem aos nomes do parâmetro de rota.

Os seguintes exemplos de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> criam rotas equivalentes:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> A sintaxe embutida para a definição de restrições e padrões pode ser interessante para rotas simples. No entanto, há cenários, como tokens de dados, que não dão suporte à sintaxe embutida.

O seguinte exemplo demonstra mais alguns cenários:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

O modelo anterior corresponde a um caminho de URL, como `/Blog/All-About-Routing/Introduction`, e extrai os valores `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Os valores de rota padrão para `controller` e `action` são produzidos pela rota, mesmo que não haja nenhum parâmetro de rota correspondente no modelo. Os valores padrão podem ser especificados no modelo de rota. O parâmetro de rota `article` é definido como um *catch-all* pela aparência de um asterisco duplo (`**`) antes do nome do parâmetro de rota. Os parâmetros de rota catch-all capturam o restante do caminho de URL e também podem corresponder à cadeia de caracteres vazia.

O seguinte exemplo adiciona restrições de rota e tokens de dados:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

O modelo anterior corresponde a um caminho de URL como `/en-US/Products/5` e extrai os valores de `{ controller = Products, action = Details, id = 5 }` e os tokens de dados `{ locale = en-US }`.

![Tokens locais do Windows](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Geração de URL da classe de rota

A classe <xref:Microsoft.AspNetCore.Routing.Route> também pode executar a geração de URL pela combinação de um conjunto de valores de rota com seu modelo de rota. Logicamente, este é o processo inverso de correspondência do caminho de URL.

> [!TIP]
> Para entender melhor a geração de URL, imagine qual URL você deseja gerar e, em seguida, pense em como um modelo de rota corresponderia a essa URL. Quais valores serão produzidos? Este é o equivalente aproximado de como funciona a geração de URL na classe <xref:Microsoft.AspNetCore.Routing.Route>.

O seguinte exemplo usa uma rota padrão geral do ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Com os valores de rota `{ controller = Products, action = List }`, a URL `/Products/List` é gerada. Os valores de rota são substituídos pelos parâmetros de rota correspondentes para formar o caminho de URL. Como `id` é um parâmetro de rota opcional, a URL é gerada com êxito sem um valor para `id`.

Com os valores de rota `{ controller = Home, action = Index }`, a URL `/` é gerada. Os valores de rota fornecidos correspondem aos valores padrão, sendo que os segmentos correspondentes aos valores padrão são omitidos com segurança.

A viagem de ida e volta gerada pelas duas URLs com a definição de rota a seguir (`/Home/Index` e `/`) produz os mesmos valores de rota que foram usados para gerar a URL.

> [!NOTE]
> Um aplicativo que usa o ASP.NET Core MVC deve usar <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> para gerar URLs, em vez de chamar o roteamento diretamente.

Para obter mais informações sobre a geração de URL, confira a seção [Referência de geração de URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Usar o middleware de roteamento

Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) no arquivo de projeto do aplicativo.

Adicione o roteamento ao contêiner de serviço em `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

As rotas precisam ser configuradas no método `Startup.Configure`. O aplicativo de exemplo usa as seguintes APIs:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Corresponde apenas às solicitações HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

A tabela a seguir mostra as respostas com os URIs fornecidos.

| URI                    | Resposta                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Olá! Valores de rota: [operation, create], [id, 3] |
| `/package/track/-3`    | Olá! Valores de rota: [operation, track], [id, -3] |
| `/package/track/-3/`   | Olá! Valores de rota: [operation, track], [id, -3] |
| `/package/track/`      | A solicitação é ignorada; sem correspondência.              |
| `GET /hello/Joe`       | Olá, Joe!                                          |
| `POST /hello/Joe`      | A solicitação é ignorada; corresponde apenas a HTTP GET. |
| `GET /hello/Joe/Smith` | A solicitação é ignorada; sem correspondência.              |

A estrutura fornece um conjunto de métodos de extensão para a criação de rotas (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Os métodos `Map[Verb]` usam restrições para limitar a rota ao Verbo HTTP no nome do método. Por exemplo, veja <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> e <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Referência de modelo de rota

Os tokens entre chaves (`{ ... }`) definem os *parâmetros de rota* que serão associados se a rota for correspondida. Você pode definir mais de um parâmetro de rota em um segmento de rota, mas eles precisam ser separados por um valor literal. Por exemplo, `{controller=Home}{action=Index}` não é uma rota válida, já que não há nenhum valor literal entre `{controller}` e `{action}`. Esses parâmetros de rota precisam ter um nome e podem ter atributos adicionais especificados.

Um texto literal diferente dos parâmetros de rota (por exemplo, `{id}`) e do separador de caminho `/` precisa corresponder ao texto na URL. A correspondência de texto não diferencia maiúsculas de minúsculas e se baseia na representação decodificada do caminho de URLs. Para encontrar a correspondência de um delimitador de parâmetro de rota literal (`{` ou `}`), faça o escape do delimitador repetindo o caractere (`{{` ou `}}`).

Padrões de URL que tentam capturar um nome de arquivo com uma extensão de arquivo opcional apresentam considerações adicionais. Por exemplo, considere o modelo `files/{filename}.{ext?}`. Quando existem valores para `filename` e `ext`, ambos os valores são populados. Se apenas existir um valor para `filename` na URL, a rota encontrará uma correspondência, pois o ponto à direita (`.`) é opcional. As URLs a seguir correspondem a essa rota:

* `/files/myFile.txt`
* `/files/myFile`

Você pode usar um asterisco (`*`) ou um asterisco duplo (`**`) como um prefixo para um parâmetro de rota para associá-lo ao restante do URI. Eles são chamados de parâmetros *catch-all*. Por exemplo, `blog/{**slug}` corresponde a qualquer URI que começa com `/blog` e tem qualquer valor depois dele, que é atribuído ao valor de rota `slug`. Os parâmetros catch-all também podem corresponder à cadeia de caracteres vazia.

O parâmetro catch-all faz o escape dos caracteres corretos quando a rota é usada para gerar uma URL, incluindo os caracteres separadores de caminho (`/`). Por exemplo, a rota `foo/{*path}` com valores de rota `{ path = "my/path" }` gera `foo/my%2Fpath`. Observe o escape da barra invertida. Para fazer a viagem de ida e volta dos caracteres separadores de caminho, use o prefixo do parâmetro da rota `**`. A rota `foo/{**path}` com `{ path = "my/path" }` gera `foo/my/path`.

Os parâmetros de rota podem ter *valores padrão*, designados pela especificação do valor padrão após o nome do parâmetro separado por um sinal de igual (`=`). Por exemplo, `{controller=Home}` define `Home` como o valor padrão de `controller`. O valor padrão é usado se nenhum valor está presente na URL para o parâmetro. Os parâmetros de rota se tornam opcionais com o acréscimo de um ponto de interrogação (`?`) ao final do nome do parâmetro, como em `id?`. A diferença entre valores opcionais e parâmetros de rota padrão é que um parâmetro de rota com um valor padrão sempre produz um valor – um parâmetro opcional tem um valor somente quando um valor é fornecido pela URL de solicitação.

Os parâmetros de rota podem ter restrições que precisam corresponder ao valor de rota associado da URL. A adição de dois-pontos (`:`) e do nome da restrição após o nome do parâmetro de rota especifica uma *restrição embutida* em um parâmetro de rota. Se a restrição exigir argumentos, eles ficarão entre parênteses (`(...)`) após o nome da restrição. Várias restrições embutidas podem ser especificadas por meio do acréscimo de outros dois-pontos (`:`) e do nome da restrição.

O nome da restrição e os argumentos são passados para o serviço <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> para criar uma instância de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a ser usada no processamento de URL. Por exemplo, o modelo de rota `blog/{article:minlength(10)}` especifica uma restrição `minlength` com o argumento `10`. Para obter mais informações sobre as restrições de rota e uma lista das restrições fornecidas pela estrutura, confira a seção [Referência de restrição de rota](#route-constraint-reference).

Os parâmetros de rota também podem ter transformadores de parâmetro, que transformam o valor de um parâmetro ao gerar links e fazer a correspondência de ações e páginas com URLs. Assim como as restrições, os transformadores de parâmetro podem ser adicionados embutidos a um parâmetro de rota colocando dois-pontos (`:`) e o nome do transformador após o nome do parâmetro de rota. Por exemplo, o modelo de rota `blog/{article:slugify}` especifica um transformador `slugify`. Para obter mais informações sobre transformadores de parâmetro, confira a seção [Referência de transformador de parâmetro](#parameter-transformer-reference).

A tabela a seguir demonstra modelos de rota de exemplo e seu comportamento.

| Modelo de rota                           | URI de correspondência de exemplo    | O URI de solicitação&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Somente corresponde ao caminho único `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Faz a correspondência e define `Page` como `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Faz a correspondência e define `Page` como `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | É mapeado para o controlador `Products` e a ação `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | É mapeado para o controlador `Products` e a ação `Details` (`id` definido como 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | É mapeado para o controlador `Home` e o método `Index` (`id` é ignorado).        |

Em geral, o uso de um modelo é a abordagem mais simples para o roteamento. Restrições e padrões também podem ser especificados fora do modelo de rota.

> [!TIP]
> Habilite o [Log](xref:fundamentals/logging/index) para ver como as implementações de roteamento internas, como <xref:Microsoft.AspNetCore.Routing.Route>, correspondem às solicitações.

## <a name="reserved-routing-names"></a>Nomes reservados de roteamento

As seguintes palavras-chave são nomes reservados e não podem ser usadas como nomes de rota ou parâmetros:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Referência de restrição de rota

As restrições de rota são executadas quando ocorre uma correspondência com a URL de entrada e é criado um token do caminho da URL em valores de rota. Em geral, as restrições da rota inspecionam o valor de rota associado por meio do modelo de rota e tomam uma decisão do tipo "sim/não" sobre se o valor é aceitável ou não. Algumas restrições da rota usam dados fora do valor de rota para considerar se a solicitação pode ser encaminhada. Por exemplo, a <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> pode aceitar ou rejeitar uma solicitação de acordo com o verbo HTTP. As restrições são usadas em solicitações de roteamento e na geração de link.

> [!WARNING]
> Não use restrições para a **validação de entrada**. Se as restrições forem usadas para a **validação de entrada**, uma entrada inválida resultará em uma resposta *404 – Não Encontrado*, em vez de *400 – Solicitação Inválida* com uma mensagem de erro apropriada. As restrições de rota são usadas para **desfazer a ambiguidade** entre rotas semelhantes, não para validar as entradas de uma rota específica.

A tabela a seguir demonstra restrições de rota de exemplo e seu comportamento esperado.

| restrição | Exemplo | Correspondências de exemplo | Observações |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Combina com qualquer inteiro. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Fósforos `true` ou falsos. Caso insensível. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Corresponde a `DateTime` um valor válido na cultura invariante. Veja o aviso anterior.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Corresponde a `decimal` um valor válido na cultura invariante. Veja o aviso anterior.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Corresponde a `double` um valor válido na cultura invariante. Veja o aviso anterior.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Corresponde a `float` um valor válido na cultura invariante. Veja o aviso anterior.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Corresponde a `Guid` um valor válido. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Corresponde a `long` um valor válido. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String deve ser de pelo menos 4 caracteres. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String tem no máximo 8 caracteres. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | A corda deve ter exatamente 12 caracteres. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | A corda deve ter pelo menos 8 e tem no máximo 16 caracteres. |
| `min(value)` | `{age:min(18)}` | `19` | O valor inteiro deve ser de pelo menos 18. |
| `max(value)` | `{age:max(120)}` | `91` | Valor total de 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | O valor inteiro deve ser de no mínimo 18 e máximo de 120. |
| `alpha` | `{name:alpha}` | `Rick` | String deve consistir de um `a` - `z`ou mais caracteres alfabéticos .  Caso insensível. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | A corda deve corresponder à expressão regular. Veja dicas sobre como definir uma expressão regular. |
| `required` | `{name:required}` | `Rick` | Usado para impor que um valor não parâmetro está presente durante a geração de URL. |

Várias restrições delimitadas por vírgula podem ser aplicadas a um único parâmetro. Por exemplo, a restrição a seguir restringe um parâmetro para um valor inteiro de 1 ou maior:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável. Essas restrições consideram que a URL não é localizável. As restrições de rota fornecidas pela estrutura não modificam os valores armazenados nos valores de rota. Todos os valores de rota analisados com base na URL são armazenados como cadeias de caracteres. Por exemplo, a restrição `float` tenta converter o valor de rota em um float, mas o valor convertido é usado somente para verificar se ele pode ser convertido em um float.

## <a name="regular-expressions"></a>Expressões regulares

A estrutura do ASP.NET Core adiciona `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` ao construtor de expressão regular. Confira <xref:System.Text.RegularExpressions.RegexOptions> para obter uma descrição desses membros.

Expressões regulares usam delimitadores e tokens semelhantes aos usados pelo roteamento e pela linguagem C#. Os tokens de expressão regular precisam ter escape. Para usar a `^\d{3}-\d{2}-\d{4}$` expressão regular no roteamento:

* A expressão deve ter `\` os caracteres de barra invertida única fornecidos na seqüência como caracteres de barra invertida `\\` dupla no código-fonte.
* A expressão regular `\\` deve-nos `\` para escapar do personagem de fuga de cordas.
* A expressão regular não `\\` requer ao usar [literais de cordas verbatim](/dotnet/csharp/language-reference/keywords/string).

Para `{`escapar dos caracteres delimitadores do parâmetro de roteamento, `}` `[`o `]`dobro dos caracteres na expressão, `{{` `}`mas `[[`. `]]` A tabela a seguir mostra uma expressão regular e a versão fugitiva:

| Expressão regular    | Expressão regular com escape     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Expressões regulares usadas no roteamento `^` geralmente começam com o caractere caret e correspondem à posição inicial da string. As expressões geralmente terminam `$` com o caractere do sinal de dólar e a extremidade da seqüência. Os caracteres `^` e `$` garantem que a expressão regular corresponde a todo o valor do parâmetro de rota. Sem os caracteres `^` e `$`, a expressão regular corresponde a qualquer subcadeia de caracteres na cadeia de caracteres, o que geralmente não é o desejado. A tabela a seguir fornece exemplos e explica por que eles encontram ou não uma correspondência.

| Expression   | String    | Corresponder a | Comentário               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Sim   | A subcadeia de caracteres corresponde     |
| `[a-z]{2}`   | 123abc456 | Sim   | A subcadeia de caracteres corresponde     |
| `[a-z]{2}`   | mz        | Sim   | Corresponde à expressão    |
| `[a-z]{2}`   | MZ        | Sim   | Não diferencia maiúsculas de minúsculas    |
| `^[a-z]{2}$` | hello     | Não    | Confira `^` e `$` acima |
| `^[a-z]{2}$` | 123abc456 | Não    | Confira `^` e `$` acima |

Para saber mais sobre a sintaxe de expressões regulares, confira [Expressões regulares do .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Para restringir um parâmetro a um conjunto conhecido de valores possíveis, use uma expressão regular. Por exemplo, `{action:regex(^(list|get|create)$)}` apenas corresponde o valor da rota `action` a `list`, `get` ou `create`. Se passada para o dicionário de restrições, a cadeia de caracteres `^(list|get|create)$` é equivalente. As restrições passadas para o dicionário de restrições (não embutidas em um modelo) que não correspondem a uma das restrições conhecidas também são tratadas como expressões regulares.

## <a name="custom-route-constraints"></a>Restrições de rota personalizadas

Além das restrições de rota internas, é possível criar restrições de rota personalizadas com a implementação da interface do <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. A interface do <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contém um único método, `Match`, que retorna `true` quando a restrição é satisfeita. Caso contrário, retorna `false`.

Para usar uma <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personalizada, o tipo de restrição de rota deve ser registrado com o <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> do aplicativo, no contêiner de serviço do aplicativo. O <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> é um dicionário que mapeia as chaves de restrição de rota para implementações de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> que validam essas restrições. É possível atualizar o <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> do aplicativo no `Startup.ConfigureServices` como parte de uma chamada [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou configurando <xref:Microsoft.AspNetCore.Routing.RouteOptions> diretamente com `services.Configure<RouteOptions>`. Por exemplo:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

a restrição pode então ser aplicada às rotas da maneira usual, usando o nome especificado ao registrar o tipo de restrição. Por exemplo:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Referência de parâmetro de transformador

Transformadores de parâmetro:

* Executar ao gerar um link para um <xref:Microsoft.AspNetCore.Routing.Route>.
* Implementar `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* São configurados usando <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Usam o valor de rota do parâmetro e o transformam em um novo valor de cadeia de caracteres.
* Resultam no uso do valor transformado no link gerado.

Por exemplo, um transformador de parâmetro `slugify` personalizado em padrão de rota `blog\{article:slugify}` com `Url.Action(new { article = "MyTestArticle" })` gera `blog\my-test-article`.

Para usar um transformador de parâmetro em um padrão de rota, configure-o primeiro usando <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> em `Startup.ConfigureServices`:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Os transformadores de parâmetro são usados pela estrutura para transformar o URI no qual um ponto de extremidade é resolvido. Por exemplo, o ASP.NET Core MVC usa os transformadores de parâmetro para transformar o valor de rota usado para corresponder a um `area`, `controller`, `action` e `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Com a rota anterior, a ação `SubscriptionManagementController.GetAll` é combinada com o URI `/subscription-management/get-all`. Um transformador de parâmetro não altera os valores de rota usados para gerar um link. Por exemplo, `Url.Action("GetAll", "SubscriptionManagement")` gera `/subscription-management/get-all`.

ASP.NET Core fornece convenções de API para usar transformadores de parâmetro com as rotas geradas:

* ASP.NET Core MVC tem a convenção de API `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention`. Essa convenção aplica um transformador de parâmetro especificado a todas as rotas de atributo no aplicativo. O transformador de parâmetro transforma os tokens de rota do atributo conforme elas são substituídas. Para obter mais informações, confira [Usar um transformador de parâmetro para personalizar a substituição de token](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* O Razor Pages tem a convenção de API `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention`. Essa convenção aplica um transformador de parâmetro especificado a todas as Razor Pages descobertas automaticamente. O transformador de parâmetro transforma os segmentos de nome de arquivo e pasta de rotas do Razor Pages. Para obter mais informações, confira [Usar um transformador de parâmetros para personalizar rotas de página](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Referência de geração de URL

O exemplo a seguir mostra como gerar um link para uma rota com base em um dicionário de valores de rota e em um <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

O <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> gerado no final do exemplo anterior é `/package/create/123`. O dicionário fornece os valores de rota `operation` e `id` do modelo "Rastrear rota do pacote", `package/{operation}/{id}`. Para obter detalhes, consulte o código de exemplo na seção [Usar o middleware de roteamento](#use-routing-middleware) ou no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

O segundo parâmetro para o construtor <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> é uma coleção de *valores de ambiente*. Os valores de ambiente são convenientes de serem usados porque limitam o número de valores que um desenvolvedor precisa especificar em um contexto de solicitação. Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link. Na ação `About` de um aplicativo ASP.NET Core MVC do `HomeController`, não é necessário especificar o valor de rota do controlador a ser vinculado à ação `Index` – o valor de ambiente `Home` é usado.

Os valores de ambiente que não correspondem a um parâmetro são ignorados. Os valores de ambiente também são ignorados quando um valor fornecido explicitamente substitui o valor de ambiente. A correspondência ocorre da esquerda para a direita na URL.

Valores fornecidos explicitamente, mas que não correspondem a um segmento da rota, são adicionados à cadeia de consulta. A tabela a seguir mostra o resultado do uso do modelo de rota `{controller}/{action}/{id?}`.

| Valores de ambiente                     | Valores explícitos                        | Result                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controlador = "Home"                | ação = "About"                       | `/Home/About`           |
| controlador = "Home"                | controlador = "Order", ação = "About" | `/Order/About`          |
| controlador = "Home", cor = "Red" | ação = "About"                       | `/Home/About`           |
| controlador = "Home"                | ação = "About", cor = "Red"        | `/Home/About?color=Red` |

Se uma rota tem um valor padrão que não corresponde a um parâmetro e esse valor é fornecido de forma explícita, ele precisa corresponder ao valor padrão:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

A geração de link somente gera um link para essa rota quando os valores correspondentes de `controller` e `action` são fornecidos.

## <a name="complex-segments"></a>Segmentos complexos

Segmentos complexos (por exemplo, `[Route("/x{token}y")]`) são processados por meio da combinação de literais da direita para a esquerda, de uma maneira diferente de Greedy. Confira [este código](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) para ver uma explicação detalhada de como os segmentos complexos são combinados. O [exemplo de código](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) não é usado pelo ASP.NET Core, mas fornece uma explicação adequada sobre segmentos complexos.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O roteamento é responsável por mapear URIs de solicitação para manipuladores de rotas e expedir as solicitações de entrada. As rotas são definidas no aplicativo e configuradas quando o aplicativo é iniciado. Uma rota pode opcionalmente extrair os valores da URL contida na solicitação e esses valores podem então ser usados para o processamento da solicitação. Usando rotas configuradas no aplicativo, o roteamento pode gerar URLs que são mapeadas para manipuladores de rotas.

Para usar os últimos cenários de roteamento no ASP.NET Core 2.1, especifique a [versão de compatibilidade](xref:mvc/compatibility-version) com o registro de serviços MVC em `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Este documento aborda o roteamento de nível inferior do ASP.NET Core. Para obter informações sobre o roteamento do ASP.NET Core MVC, confira <xref:mvc/controllers/routing>. Para obter informações sobre convenções de roteamento no Razor Pages, confira <xref:razor-pages/razor-pages-conventions>.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Conceitos básicos sobre roteamento

A maioria dos aplicativos deve escolher um esquema de roteamento básico e descritivo para que as URLs sejam legíveis e significativas. A rota convencional padrão `{controller=Home}/{action=Index}/{id?}`:

* Dá suporte a um esquema de roteamento básico e descritivo.
* É um ponto de partida útil para aplicativos baseados em interface do usuário.

Os desenvolvedores geralmente adicionam outras rotas concisas às áreas de alto tráfego de um aplicativo em situações especiais (por exemplo, pontos de extremidade de blog e comércio eletrônico) usando o [roteamento de atributo](xref:mvc/controllers/routing#attribute-routing) ou rotas convencionais dedicadas.

As APIs da Web devem usar o roteamento de atributo para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por verbos HTTP. Isso significa que muitas operações (por exemplo, GET, POST) no mesmo recurso lógico usarão a mesma URL. O roteamento de atributo fornece um nível de controle necessário para projetar cuidadosamente o layout de ponto de extremidade público de uma API.

Os aplicativos do Razor Pages usam o roteamento convencional padrão para fornecer recursos nomeados na pasta *Pages* de um aplicativo. Estão disponíveis convenções adicionais que permitem a personalização do comportamento de roteamento do Razor Pages. Para obter mais informações, consulte <xref:razor-pages/index> e <xref:razor-pages/razor-pages-conventions>.

O suporte à geração de URL permite que o aplicativo seja desenvolvido sem hard-coding das URLs para vincular o aplicativo. Esse suporte permite começar com uma configuração de roteamento básica e modificar as rotas, depois que o layout de recurso do aplicativo é determinado.

O roteamento utiliza <xref:Microsoft.AspNetCore.Routing.IRouter> implementações de rotas de:

* Mapear solicitações de entrada para *manipuladores de rotas*.
* Gerar as URLs usadas nas respostas.

Por padrão, um aplicativo tem uma única coleção de rotas. Quando uma solicitação é recebida, as rotas na coleção são processadas na ordem em que existem na coleção. A estrutura tenta corresponder uma URL de solicitação de entrada a uma rota na coleção chamando o método <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> em cada rota da coleção. Uma resposta pode usar o roteamento para gerar URLs (por exemplo, para redirecionamento ou links) com base nas informações de rotas e evitar URLs embutidas em código, o que ajuda na facilidade de manutenção.

O sistema de roteamento tem as seguintes características:

* A sintaxe do modelo de rota é usada para definir rotas com os parâmetros de rota com tokens criados.
* A configuração de ponto de extremidade de estilo convencional e de estilo de atributo é permitida.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> é usado para determinar se um parâmetro de URL contém um valor válido para determinada restrição de ponto de extremidade.
* Modelos de aplicativo, como MVC/Razor Pages, registram todas as suas rotas, que têm uma implementação previsível de cenários de roteamento.
* Uma resposta pode usar o roteamento para gerar URLs (por exemplo, para redirecionamento ou links) com base nas informações de rotas e evitar URLs embutidas em código, o que ajuda na facilidade de manutenção.
* A geração de URL baseia-se em rotas, que dá suporte à extensibilidade arbitrária. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> oferece métodos para criar URLs.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
O roteamento está conectado ao pipeline do [middleware](xref:fundamentals/middleware/index) pela classe <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>. O [ASP.NET Core MVC](xref:mvc/overview) adiciona o roteamento ao pipeline de middleware como parte de sua configuração e manipula o roteamento nos aplicativos do MVC e do Razor Pages. Para saber como usar o roteamento como um componente autônomo, confira a seção [Usar o Middleware de Roteamento](#use-routing-middleware).

### <a name="url-matching"></a>Correspondência de URL

Correspondência de URL é o processo pelo qual o roteamento expede uma solicitação de entrada para um *manipulador*. Esse processo se baseia nos dados do caminho da URL, mas pode ser estendido para considerar qualquer dado na solicitação. A capacidade de expedir solicitações para manipuladores separados é fundamental para dimensionar o tamanho e a complexidade de um aplicativo.

As solicitações de entrada entram no <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, que chama o método <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> em cada rota na sequência. A instância <xref:Microsoft.AspNetCore.Routing.IRouter> escolhe se deseja *manipular* a solicitação definindo o [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) como um <xref:Microsoft.AspNetCore.Http.RequestDelegate> não nulo. Se uma rota definir um manipulador para a solicitação, o processamento de rotas será interrompido e o manipulador será invocado para processar a solicitação. Se nenhum manipulador de rotas é encontrado para processar a solicitação, o middleware transmite a solicitação para o próximo middleware no pipeline de solicitação.

A entrada primária para <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> é o [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associado à solicitação atual. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) e [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) são saídas definidas depois que é encontrada uma correspondência de uma rota.

Uma correspondência que chama <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> também define as propriedades do [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) com valores apropriados com base no processamento da solicitação executado até o momento.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) é um dicionário de *valores de rota* produzido por meio da rota. Esses valores geralmente são determinados pela criação de token da URL e podem ser usados para aceitar a entrada do usuário ou tomar outras decisões de expedição dentro do aplicativo.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) é um recipiente de propriedades de dados adicionais relacionados à rota correspondente. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> são fornecidos para dar suporte à associação de dados de estado com cada rota para que o aplicativo possa tomar decisões com base em qual rota teve uma correspondência. Esses valores são definidos pelo desenvolvedor e **não** afetam de forma alguma o comportamento do roteamento. Além disso, os valores em stash em [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) podem ser de qualquer tipo, ao contrário de [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), que devem ser conversíveis de/para cadeias de caracteres.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) é uma lista das rotas que participaram da correspondência bem-sucedida da solicitação. As rotas podem ser aninhadas uma dentro da outra. A propriedade <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> reflete o caminho pela árvore lógica de rotas que resultou em uma correspondência. Em geral, o primeiro item em <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> é a coleção de rotas e deve ser usado para a geração de URL. O último item em <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> é o manipulador de rotas que teve uma correspondência.

<a name="lg"></a>

### <a name="url-generation"></a>Geração de URL

Geração de URL é o processo pelo qual o roteamento pode criar um caminho de URL de acordo com um conjunto de valores de rota. Isso permite uma separação lógica entre os manipuladores de rotas e as URLs que os acessam.

A geração de URL segue um processo iterativo semelhante, mas começa com o código da estrutura ou do usuário chamando o método <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> da coleção de rotas. Cada *rota* tem seu método <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> chamado em sequência, até que um <xref:Microsoft.AspNetCore.Routing.VirtualPathData> não nulo seja retornado.

As entradas primárias para <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> são:

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

As rotas usam principalmente os valores de rota fornecidos por <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> e <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> para decidir se é possível gerar uma URL e quais valores serão incluídos. Os <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> são o conjunto de valores de rota produzidos pela correspondência da solicitação atual. Por outro lado, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> são os valores de rota que especificam como gerar a URL desejada para a operação atual. O <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> é fornecido para o caso de uma rota precisar obter serviços ou dados adicionais associados ao contexto atual.

> [!TIP]
> Considere [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) como um conjunto de substituições de [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). A geração de URL tenta reutilizar os valores de rota da solicitação atual para gerar URLs para links usando a mesma rota ou os mesmos valores de rota.

A saída de <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> é um <xref:Microsoft.AspNetCore.Routing.VirtualPathData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> é um paralelo de <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> contém o <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> da URL de saída e algumas propriedades adicionais que devem ser definidas pela rota.

A propriedade [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) contém o *caminho virtual* produzido pela rota. Dependendo das suas necessidades, talvez você precise processar ainda mais o caminho. Se você desejar renderizar a URL gerada em HTML, preceda o caminho base do aplicativo.

O [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) é uma referência à rota que gerou a URL com êxito.

As propriedades de [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) são um dicionário de dados adicionais relacionados à rota que gerou a URL. Isso é o paralelo de [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Criar rotas

O roteamento fornece a classe <xref:Microsoft.AspNetCore.Routing.Route> como a implementação padrão de <xref:Microsoft.AspNetCore.Routing.IRouter>. <xref:Microsoft.AspNetCore.Routing.Route> usa a sintaxe de *modelo de rota* para definir padrões que corresponderão ao caminho da URL quando <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> for chamado. <xref:Microsoft.AspNetCore.Routing.Route> usa o mesmo modelo de rota para gerar uma URL quando <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> é chamado.

A maioria dos aplicativos cria rotas chamando <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ou um dos métodos de extensão semelhantes definidos em <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Qualquer um dos métodos de extensão de <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> cria uma instância de <xref:Microsoft.AspNetCore.Routing.Route> e a adicionam à coleção de rotas.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> não aceita um parâmetro de manipulador de rotas. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> apenas adiciona rotas que são manipuladas pelo <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. O manipulador padrão é um `IRouter`, e o manipulador não pode manipular a solicitação. Por exemplo, o ASP.NET Core MVC normalmente é configurado como um manipulador padrão que só manipula as solicitações que correspondem a um controlador e a uma ação disponíveis. Para saber mais sobre o roteamento no MVC, confira <xref:mvc/controllers/routing>.

O exemplo de código a seguir é um exemplo de uma chamada <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> usada por uma definição de rota típica do ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Esse modelo corresponde a um caminho de URL e extrai os valores de rota. Por exemplo, o caminho `/Products/Details/17` gera os seguintes valores de rota: `{ controller = Products, action = Details, id = 17 }`.

Os valores de rota são determinados pela divisão do caminho da URL em segmentos e pela correspondência de cada segmento com o nome do *parâmetro de rota* no modelo de rota. Os parâmetros de rota são nomeados. Os parâmetros são definidos com a colocação do nome do parâmetro em chaves `{ ... }`.

O modelo anterior também pode corresponder ao caminho da URL `/` e produzir os valores `{ controller = Home, action = Index }`. Isso ocorre porque os parâmetros de rota `{controller}` e `{action}` têm valores padrão e o parâmetro de rota `id` é opcional. Um sinal de igual (`=`) seguido de um valor após o nome do parâmetro de rota define um valor padrão para o parâmetro. Um ponto de interrogação (`?`) após o nome do parâmetro de rota define o parâmetro como opcional.

Os parâmetros de rota com um valor padrão *sempre* produzem um valor de rota quando a rota corresponde. Os parâmetros opcionais não produzem um valor de rota se não houver um segmento de caminho de URL correspondente. Confira a seção [Referência de modelo de rota](#route-template-reference) para obter uma descrição completa dos recursos e da sintaxe de modelo de rota.

No seguinte exemplo, a definição do parâmetro de rota `{id:int}` define uma [restrição de rota](#route-constraint-reference) para o parâmetro de rota `id`:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Esse modelo corresponde a um caminho de URL, como `/Products/Details/17`, mas não como `/Products/Details/Apples`. As restrições de rota implementam <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> e inspecionam valores de rota para verificá-los. Neste exemplo, o valor de rota `id` precisa ser conversível em um inteiro. Confira [route-constraint-reference](#route-constraint-reference) para obter uma explicação das restrições de rota fornecidas pela estrutura.

Sobrecargas adicionais de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> aceitam valores para `constraints`, `dataTokens` e `defaults`. O uso típico desses parâmetros é passar um objeto de tipo anônimo, no qual os nomes da propriedade do tipo anônimo correspondem aos nomes do parâmetro de rota.

Os seguintes exemplos de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> criam rotas equivalentes:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> A sintaxe embutida para a definição de restrições e padrões pode ser interessante para rotas simples. No entanto, há cenários, como tokens de dados, que não dão suporte à sintaxe embutida.

O seguinte exemplo demonstra mais alguns cenários:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

O modelo anterior corresponde a um caminho de URL, como `/Blog/All-About-Routing/Introduction`, e extrai os valores `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Os valores de rota padrão para `controller` e `action` são produzidos pela rota, mesmo que não haja nenhum parâmetro de rota correspondente no modelo. Os valores padrão podem ser especificados no modelo de rota. O parâmetro de rota `article` é definido como um *catch-all* pela aparência de um asterisco (`*`) antes do nome do parâmetro de rota. Os parâmetros de rota catch-all capturam o restante do caminho de URL e também podem corresponder à cadeia de caracteres vazia.

O seguinte exemplo adiciona restrições de rota e tokens de dados:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

O modelo anterior corresponde a um caminho de URL como `/en-US/Products/5` e extrai os valores de `{ controller = Products, action = Details, id = 5 }` e os tokens de dados `{ locale = en-US }`.

![Tokens locais do Windows](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Geração de URL da classe de rota

A classe <xref:Microsoft.AspNetCore.Routing.Route> também pode executar a geração de URL pela combinação de um conjunto de valores de rota com seu modelo de rota. Logicamente, este é o processo inverso de correspondência do caminho de URL.

> [!TIP]
> Para entender melhor a geração de URL, imagine qual URL você deseja gerar e, em seguida, pense em como um modelo de rota corresponderia a essa URL. Quais valores serão produzidos? Este é o equivalente aproximado de como funciona a geração de URL na classe <xref:Microsoft.AspNetCore.Routing.Route>.

O seguinte exemplo usa uma rota padrão geral do ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Com os valores de rota `{ controller = Products, action = List }`, a URL `/Products/List` é gerada. Os valores de rota são substituídos pelos parâmetros de rota correspondentes para formar o caminho de URL. Como `id` é um parâmetro de rota opcional, a URL é gerada com êxito sem um valor para `id`.

Com os valores de rota `{ controller = Home, action = Index }`, a URL `/` é gerada. Os valores de rota fornecidos correspondem aos valores padrão, sendo que os segmentos correspondentes aos valores padrão são omitidos com segurança.

A viagem de ida e volta gerada pelas duas URLs com a definição de rota a seguir (`/Home/Index` e `/`) produz os mesmos valores de rota que foram usados para gerar a URL.

> [!NOTE]
> Um aplicativo que usa o ASP.NET Core MVC deve usar <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> para gerar URLs, em vez de chamar o roteamento diretamente.

Para obter mais informações sobre a geração de URL, confira a seção [Referência de geração de URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Use middleware de roteamento

Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) no arquivo de projeto do aplicativo.

Adicione o roteamento ao contêiner de serviço em `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

As rotas precisam ser configuradas no método `Startup.Configure`. O aplicativo de exemplo usa as seguintes APIs:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Corresponde apenas às solicitações HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

A tabela a seguir mostra as respostas com os URIs fornecidos.

| URI                    | Resposta                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Olá! Valores de rota: [operation, create], [id, 3] |
| `/package/track/-3`    | Olá! Valores de rota: [operation, track], [id, -3] |
| `/package/track/-3/`   | Olá! Valores de rota: [operation, track], [id, -3] |
| `/package/track/`      | A solicitação é ignorada; sem correspondência.              |
| `GET /hello/Joe`       | Olá, Joe!                                          |
| `POST /hello/Joe`      | A solicitação é ignorada; corresponde apenas a HTTP GET. |
| `GET /hello/Joe/Smith` | A solicitação é ignorada; sem correspondência.              |

Se você estiver configurando uma única rota, chame <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passando uma instância de `IRouter`. Você não precisa usar <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

A estrutura fornece um conjunto de métodos de extensão para a criação de rotas (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Alguns dos métodos listados, como <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, exigem um <xref:Microsoft.AspNetCore.Http.RequestDelegate>. O <xref:Microsoft.AspNetCore.Http.RequestDelegate> é usado como o *manipulador de rotas* quando a rota corresponde. Outros métodos nesta família permitem configurar um pipeline de middleware a ser usado como o manipulador de rotas. Se o método `Map*` não aceitar um manipulador, como <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, ele usará o <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.

Os métodos `Map[Verb]` usam restrições para limitar a rota ao Verbo HTTP no nome do método. Por exemplo, veja <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> e <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Referência de modelo de rota

Os tokens entre chaves (`{ ... }`) definem os *parâmetros de rota* que serão associados se a rota for correspondida. Você pode definir mais de um parâmetro de rota em um segmento de rota, mas eles precisam ser separados por um valor literal. Por exemplo, `{controller=Home}{action=Index}` não é uma rota válida, já que não há nenhum valor literal entre `{controller}` e `{action}`. Esses parâmetros de rota precisam ter um nome e podem ter atributos adicionais especificados.

Um texto literal diferente dos parâmetros de rota (por exemplo, `{id}`) e do separador de caminho `/` precisa corresponder ao texto na URL. A correspondência de texto não diferencia maiúsculas de minúsculas e se baseia na representação decodificada do caminho de URLs. Para encontrar a correspondência de um delimitador de parâmetro de rota literal (`{` ou `}`), faça o escape do delimitador repetindo o caractere (`{{` ou `}}`).

Padrões de URL que tentam capturar um nome de arquivo com uma extensão de arquivo opcional apresentam considerações adicionais. Por exemplo, considere o modelo `files/{filename}.{ext?}`. Quando existem valores para `filename` e `ext`, ambos os valores são populados. Se apenas existir um valor para `filename` na URL, a rota encontrará uma correspondência, pois o ponto à direita (`.`) é opcional. As URLs a seguir correspondem a essa rota:

* `/files/myFile.txt`
* `/files/myFile`

Você pode usar o asterisco (`*`) como um prefixo para um parâmetro de rota a ser associado ao restante do URI. Isso é chamado de parâmetro *catch-all*. Por exemplo, `blog/{*slug}` corresponde a qualquer URI que começa com `/blog` e tem qualquer valor depois dele, que é atribuído ao valor de rota `slug`. Os parâmetros catch-all também podem corresponder à cadeia de caracteres vazia.

O parâmetro catch-all faz o escape dos caracteres corretos quando a rota é usada para gerar uma URL, incluindo os caracteres separadores de caminho (`/`). Por exemplo, a rota `foo/{*path}` com valores de rota `{ path = "my/path" }` gera `foo/my%2Fpath`. Observe o escape da barra invertida.

Os parâmetros de rota podem ter *valores padrão*, designados pela especificação do valor padrão após o nome do parâmetro separado por um sinal de igual (`=`). Por exemplo, `{controller=Home}` define `Home` como o valor padrão de `controller`. O valor padrão é usado se nenhum valor está presente na URL para o parâmetro. Os parâmetros de rota se tornam opcionais com o acréscimo de um ponto de interrogação (`?`) ao final do nome do parâmetro, como em `id?`. A diferença entre valores opcionais e parâmetros de rota padrão é que um parâmetro de rota com um valor padrão sempre produz um valor – um parâmetro opcional tem um valor somente quando um valor é fornecido pela URL de solicitação.

Os parâmetros de rota podem ter restrições que precisam corresponder ao valor de rota associado da URL. A adição de dois-pontos (`:`) e do nome da restrição após o nome do parâmetro de rota especifica uma *restrição embutida* em um parâmetro de rota. Se a restrição exigir argumentos, eles ficarão entre parênteses (`(...)`) após o nome da restrição. Várias restrições embutidas podem ser especificadas por meio do acréscimo de outros dois-pontos (`:`) e do nome da restrição.

O nome da restrição e os argumentos são passados para o serviço <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> para criar uma instância de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a ser usada no processamento de URL. Por exemplo, o modelo de rota `blog/{article:minlength(10)}` especifica uma restrição `minlength` com o argumento `10`. Para obter mais informações sobre as restrições de rota e uma lista das restrições fornecidas pela estrutura, confira a seção [Referência de restrição de rota](#route-constraint-reference).

A tabela a seguir demonstra modelos de rota de exemplo e seu comportamento.

| Modelo de rota                           | URI de correspondência de exemplo    | O URI de solicitação&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Somente corresponde ao caminho único `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Faz a correspondência e define `Page` como `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Faz a correspondência e define `Page` como `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | É mapeado para o controlador `Products` e a ação `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | É mapeado para o controlador `Products` e a ação `Details` (`id` definido como 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | É mapeado para o controlador `Home` e o método `Index` (`id` é ignorado).        |

Em geral, o uso de um modelo é a abordagem mais simples para o roteamento. Restrições e padrões também podem ser especificados fora do modelo de rota.

> [!TIP]
> Habilite o [Log](xref:fundamentals/logging/index) para ver como as implementações de roteamento internas, como <xref:Microsoft.AspNetCore.Routing.Route>, correspondem às solicitações.

## <a name="route-constraint-reference"></a>Referência de restrição de rota

As restrições de rota são executadas quando ocorre uma correspondência com a URL de entrada e é criado um token do caminho da URL em valores de rota. Em geral, as restrições da rota inspecionam o valor de rota associado por meio do modelo de rota e tomam uma decisão do tipo "sim/não" sobre se o valor é aceitável ou não. Algumas restrições da rota usam dados fora do valor de rota para considerar se a solicitação pode ser encaminhada. Por exemplo, a <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> pode aceitar ou rejeitar uma solicitação de acordo com o verbo HTTP. As restrições são usadas em solicitações de roteamento e na geração de link.

> [!WARNING]
> Não use restrições para a **validação de entrada**. Se as restrições forem usadas para a **validação de entrada**, uma entrada inválida resultará em uma resposta *404 – Não Encontrado*, em vez de *400 – Solicitação Inválida* com uma mensagem de erro apropriada. As restrições de rota são usadas para **desfazer a ambiguidade** entre rotas semelhantes, não para validar as entradas de uma rota específica.

A tabela a seguir demonstra restrições de rota de exemplo e seu comportamento esperado.

| restrição | Exemplo | Correspondências de exemplo | Observações |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Corresponde a qualquer inteiro |
| `bool` | `{active:bool}` | `true`, `FALSE` | Corresponde a `true` ou `false` (não diferencia maiúsculas de minúsculas) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Corresponde a `DateTime` um valor válido na cultura invariante. Veja o aviso anterior.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Corresponde a `decimal` um valor válido na cultura invariante. Veja o aviso anterior.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Corresponde a `double` um valor válido na cultura invariante. Veja o aviso anterior.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Corresponde a `float` um valor válido na cultura invariante. Veja o aviso anterior.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Corresponde a um valor `Guid` válido |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Corresponde a um valor `long` válido |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | A cadeia de caracteres deve ter, no mínimo, 4 caracteres |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | A cadeia de caracteres não pode ser maior que 8 caracteres |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | A cadeia de caracteres deve ter exatamente 12 caracteres |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | A cadeia de caracteres deve ter, pelo menos, 8 e não mais de 16 caracteres |
| `min(value)` | `{age:min(18)}` | `19` | O valor inteiro deve ser, pelo menos, 18 |
| `max(value)` | `{age:max(120)}` | `91` | O valor inteiro não deve ser maior que 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | O valor inteiro deve ser, pelo menos, 18, mas não maior que 120 |
| `alpha` | `{name:alpha}` | `Rick` | A cadeia de caracteres deve consistir em um ou mais caracteres alfabéticos (`a`-`z`, não diferencia maiúsculas de minúsculas) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | A cadeia de caracteres deve corresponder à expressão regular (veja as dicas sobre como definir uma expressão regular) |
| `required` | `{name:required}` | `Rick` | Usado para impor que um valor não parâmetro está presente durante a geração de URL |

Várias restrições delimitadas por vírgula podem ser aplicadas a um único parâmetro. Por exemplo, a restrição a seguir restringe um parâmetro para um valor inteiro de 1 ou maior:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável. Essas restrições consideram que a URL não é localizável. As restrições de rota fornecidas pela estrutura não modificam os valores armazenados nos valores de rota. Todos os valores de rota analisados com base na URL são armazenados como cadeias de caracteres. Por exemplo, a restrição `float` tenta converter o valor de rota em um float, mas o valor convertido é usado somente para verificar se ele pode ser convertido em um float.

## <a name="regular-expressions"></a>Expressões regulares

A estrutura do ASP.NET Core adiciona `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` ao construtor de expressão regular. Confira <xref:System.Text.RegularExpressions.RegexOptions> para obter uma descrição desses membros.

As expressões regulares usam delimitadores e tokens semelhantes aos usados pelo Roteamento e pela linguagem C#. Os tokens de expressão regular precisam ter escape. Para usar a expressão regular `^\d{3}-\d{2}-\d{4}$` no roteamento, a expressão precisa ter os caracteres `\` (barra invertida) fornecidos na cadeia de caracteres como caracteres `\\` (barra invertida dupla) no arquivo de origem C# para fazer o escape do caractere de escape da cadeia de caracteres `\` (a menos que estejam sendo usados [literais de cadeia de caracteres textuais](/dotnet/csharp/language-reference/keywords/string)). Para fazer o escape dos caracteres de delimitador de parâmetro de roteamento (`{`, `}`, `[`, `]`), duplique os caracteres na expressão (`{{`, `}`, `[[`, `]]`). A tabela a seguir mostra uma expressão regular e a versão com escape.

| Expressão regular    | Expressão regular com escape     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

As expressões regulares usadas no roteamento geralmente começam com o caractere de acento circunflexo (`^`) e correspondem à posição inicial da cadeia de caracteres. As expressões geralmente terminam com o caractere de cifrão (`$`) e correspondem ao final da cadeia de caracteres. Os caracteres `^` e `$` garantem que a expressão regular corresponde a todo o valor do parâmetro de rota. Sem os caracteres `^` e `$`, a expressão regular corresponde a qualquer subcadeia de caracteres na cadeia de caracteres, o que geralmente não é o desejado. A tabela a seguir fornece exemplos e explica por que eles encontram ou não uma correspondência.

| Expression   | String    | Corresponder a | Comentário               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Sim   | A subcadeia de caracteres corresponde     |
| `[a-z]{2}`   | 123abc456 | Sim   | A subcadeia de caracteres corresponde     |
| `[a-z]{2}`   | mz        | Sim   | Corresponde à expressão    |
| `[a-z]{2}`   | MZ        | Sim   | Não diferencia maiúsculas de minúsculas    |
| `^[a-z]{2}$` | hello     | Não    | Confira `^` e `$` acima |
| `^[a-z]{2}$` | 123abc456 | Não    | Confira `^` e `$` acima |

Para saber mais sobre a sintaxe de expressões regulares, confira [Expressões regulares do .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Para restringir um parâmetro a um conjunto conhecido de valores possíveis, use uma expressão regular. Por exemplo, `{action:regex(^(list|get|create)$)}` apenas corresponde o valor da rota `action` a `list`, `get` ou `create`. Se passada para o dicionário de restrições, a cadeia de caracteres `^(list|get|create)$` é equivalente. As restrições passadas para o dicionário de restrições (não embutidas em um modelo) que não correspondem a uma das restrições conhecidas também são tratadas como expressões regulares.

## <a name="custom-route-constraints"></a>Restrições de rota personalizadas

Além das restrições de rota internas, é possível criar restrições de rota personalizadas com a implementação da interface do <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. A interface do <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contém um único método, `Match`, que retorna `true` quando a restrição é satisfeita. Caso contrário, retorna `false`.

Para usar uma <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personalizada, o tipo de restrição de rota deve ser registrado com o <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> do aplicativo, no contêiner de serviço do aplicativo. O <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> é um dicionário que mapeia as chaves de restrição de rota para implementações de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> que validam essas restrições. É possível atualizar o <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> do aplicativo no `Startup.ConfigureServices` como parte de uma chamada [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou configurando <xref:Microsoft.AspNetCore.Routing.RouteOptions> diretamente com `services.Configure<RouteOptions>`. Por exemplo:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

a restrição pode então ser aplicada às rotas da maneira usual, usando o nome especificado ao registrar o tipo de restrição. Por exemplo:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>Referência de geração de URL

O exemplo a seguir mostra como gerar um link para uma rota com base em um dicionário de valores de rota e em um <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

O <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> gerado no final do exemplo anterior é `/package/create/123`. O dicionário fornece os valores de rota `operation` e `id` do modelo "Rastrear rota do pacote", `package/{operation}/{id}`. Para obter detalhes, consulte o código de exemplo na seção [Usar o middleware de roteamento](#use-routing-middleware) ou no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

O segundo parâmetro para o construtor <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> é uma coleção de *valores de ambiente*. Os valores de ambiente são convenientes de serem usados porque limitam o número de valores que um desenvolvedor precisa especificar em um contexto de solicitação. Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link. Na ação `About` de um aplicativo ASP.NET Core MVC do `HomeController`, não é necessário especificar o valor de rota do controlador a ser vinculado à ação `Index` – o valor de ambiente `Home` é usado.

Os valores de ambiente que não correspondem a um parâmetro são ignorados. Os valores de ambiente também são ignorados quando um valor fornecido explicitamente substitui o valor de ambiente. A correspondência ocorre da esquerda para a direita na URL.

Valores fornecidos explicitamente, mas que não correspondem a um segmento da rota, são adicionados à cadeia de consulta. A tabela a seguir mostra o resultado do uso do modelo de rota `{controller}/{action}/{id?}`.

| Valores de ambiente                     | Valores explícitos                        | Result                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controlador = "Home"                | ação = "About"                       | `/Home/About`           |
| controlador = "Home"                | controlador = "Order", ação = "About" | `/Order/About`          |
| controlador = "Home", cor = "Red" | ação = "About"                       | `/Home/About`           |
| controlador = "Home"                | ação = "About", cor = "Red"        | `/Home/About?color=Red` |

Se uma rota tem um valor padrão que não corresponde a um parâmetro e esse valor é fornecido de forma explícita, ele precisa corresponder ao valor padrão:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

A geração de link somente gera um link para essa rota quando os valores correspondentes de `controller` e `action` são fornecidos.

## <a name="complex-segments"></a>Segmentos complexos

Segmentos complexos (por exemplo, `[Route("/x{token}y")]`) são processados por meio da combinação de literais da direita para a esquerda, de uma maneira diferente de Greedy. Confira [este código](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) para ver uma explicação detalhada de como os segmentos complexos são combinados. O [exemplo de código](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) não é usado pelo ASP.NET Core, mas fornece uma explicação adequada sobre segmentos complexos.

::: moniker-end
