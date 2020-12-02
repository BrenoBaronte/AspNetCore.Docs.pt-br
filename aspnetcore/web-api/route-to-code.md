---
title: APIs JSON básicas com Route-to-code no ASP.NET Core
author: jamesnk
description: Saiba como usar os Route-to-code métodos de extensão JSON e para criar APIs da Web JSON leves.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 1f5f532053f8f5ca7f73df8c1a910a484e2488d9
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513090"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>APIs JSON básicas com Route-to-code no ASP.NET Core

Por [James Newton – King](https://github.com/jamesnk)

O ASP.NET Core dá suporte a várias maneiras de criar APIs Web JSON:

* [ASP.NET Core API Web](xref:web-api/index) fornece uma estrutura completa para a criação de APIs. Um serviço é criado pela herança de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Alguns recursos fornecidos pela estrutura incluem Associação de modelo, validação, negociação de conteúdo, formatação de entrada e saída e OpenAPI.
* Route-to-code é uma alternativa não estruturada para ASP.NET Core API Web. Route-to-code conecta [ASP.NET Core roteamento](xref:fundamentals/routing) diretamente ao seu código. Seu código lê a partir da solicitação e grava a resposta. Route-to-code Não tem recursos avançados da API Web, mas também não há nenhuma configuração necessária para usá-la.

Route-to-code é uma boa abordagem ao criar APIs Web JSON pequenas e básicas.

## <a name="create-json-web-apis"></a>Criar APIs Web JSON

ASP.NET Core fornece métodos auxiliares que facilitam a criação de APIs Web JSON:

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> verifica o `Content-Type` cabeçalho de um tipo de conteúdo JSON.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> lê o JSON da solicitação e o desserializa para o tipo especificado.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> grava o valor especificado como JSON no corpo da resposta e define o tipo de conteúdo da resposta como `application/json` .

As APIs de JSON leves baseadas em rota são especificadas em *Startup.cs*. A rota e a lógica da API são configuradas <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> como parte do pipeline de solicitação de um aplicativo.

### <a name="write-json-response"></a>Gravar resposta JSON

Considere o seguinte código que configura uma API JSON para um aplicativo:

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

O código anterior:

* Adiciona um ponto de extremidade de API HTTP GET ao `/hello/{name:alpha}` como o modelo de rota.
* Quando a rota for correspondida, a API lerá o `name` valor de rota da solicitação.
* Grava um tipo anônimo como uma resposta JSON com `WriteAsJsonAsync` .

### <a name="read-json-request"></a>Ler solicitação JSON

`HasJsonContentType` e `ReadFromJsonAsync` pode ser usado para desserializar uma resposta JSON em uma API JSON baseada em rota:

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

O código anterior:

* Adiciona um ponto de extremidade de API HTTP POST ao `/weather` como o modelo de rota.
* Quando a rota for correspondida, `HasJsonContentType` o validará o tipo de conteúdo da solicitação. Um tipo de conteúdo não JSON retorna um código de status 415.
* Se o tipo de conteúdo for JSON, o conteúdo da solicitação será desserializado pelo `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>Configurar a serialização JSON

Há duas maneiras de personalizar a serialização JSON:

* As opções de serialização padrão podem ser configuradas com `JsonOptions` o no `Startup.ConfigureServices` método.
* `WriteAsJsonAsync` e `ReadFromJsonAsync` têm sobrecargas que aceitam um `JsonSerializerOptions` objeto. Esse `JsonSerializerOptions` objeto substitui as opções padrão.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Route-to-code dá suporte à autenticação e autorização. Atributos, como `[Authorize]` e `[AllowAnonymous]` , não podem ser colocados em pontos de extremidade que são mapeados para um delegado de solicitação. Em vez disso, os metadados de autorização são adicionados usando os `RequireAuthorization` `AllowAnonymous` métodos de extensão e.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>Injeção de dependência

[Injeção de dependência (di)](xref:fundamentals/dependency-injection) usando um construtor não é possível com Route-to-code . A API da Web cria um controlador para você com os serviços injetados no construtor. Um tipo não é criado quando um ponto de extremidade é executado, de modo que os serviços devem ser resolvidos manualmente.

As APIs baseadas em rota podem usar o <xref:System.IServiceProvider> para resolver serviços:

* Serviços de tempo de vida transitórios e com escopo definido, como `DbContext` , devem ser resolvidos de [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) dentro do delegado de solicitação de um ponto de extremidade.
* Os serviços de vida útil singleton, como `ILogger` , podem ser resolvidos em [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider). Os serviços podem ser resolvidos fora dos delegados de solicitação e compartilhados entre os pontos de extremidade.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

As APIs que usam a DI extensivamente devem considerar o uso de um tipo de aplicativo ASP.NET Core que dá suporte a DI. Por exemplo, ASP.NET Core API da Web. A injeção de serviço usando um construtor de controlador é mais fácil do que resolver manualmente os serviços.

## <a name="api-project-structure"></a>Estrutura do projeto de API

As APIs baseadas em rota não precisam estar localizadas em *Startup.cs*. As APIs podem ser colocadas em outros arquivos e mapeadas na inicialização com o `UseEndpoints` . Essa abordagem reduz o tamanho do arquivo de configuração de inicialização.

Considere a seguinte `UserApi` classe estática que define um `Map` método. O método mapeia as APIs baseadas em rota.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

No `Startup.Configure` método, o `Map` método e os métodos estáticos de outras classes são chamados em `UseEndpoints` :

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Recursos ausentes notáveis em comparação com a API da Web

Route-to-code o é projetado para as APIs JSON básicas. Ele não tem suporte para muitos dos recursos avançados fornecidos pela API Web do ASP.NET Core.

Recursos não fornecidos pelo Route-to-code incluem:

* Model binding
* Validação de modelo
* OpenAPI/Swagger
* Negociação de conteúdo
* Injeção de dependência de Construtor
* `ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))

Considere usar [ASP.NET Core API Web](xref:web-api/index) para criar uma API se ela exigir alguns dos recursos na lista anterior.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
