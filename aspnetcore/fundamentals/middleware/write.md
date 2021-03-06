---
title: Escrever middleware do ASP.NET Core personalizado
author: rick-anderson
description: Saiba como escrever middleware do ASP.NET Core personalizado.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
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
uid: fundamentals/middleware/write
ms.openlocfilehash: 5f33691cbcc00f407fff907ca62547fd80f2aa3c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057460"
---
# <a name="write-custom-aspnet-core-middleware"></a>Escrever middleware do ASP.NET Core personalizado

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

O middleware é um software montado em um pipeline de aplicativo para manipular solicitações e respostas. O ASP.NET Core fornece um rico conjunto de componentes de middleware internos, mas em alguns cenários, talvez seja conveniente que você escreva um middleware personalizado.

> [!NOTE]
> Este tópico descreve como escrever middleware *baseado em Convenção* . Para obter uma abordagem que usa rigidez de tipos e ativação por solicitação, consulte <xref:fundamentals/middleware/extensibility> .

## <a name="middleware-class"></a>Classe de middleware

O middleware geralmente é encapsulado em uma classe e exposto com um método de extensão. Considere o middleware a seguir, que define a cultura para a solicitação atual de uma cadeia de caracteres de consulta:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

O código de exemplo anterior é usado para demonstrar a criação de um componente de middleware. Para suporte de localização interna do ASP.NET Core, veja <xref:fundamentals/localization>.

Teste o middleware ao transmitir a cultura. Por exemplo, solicite `https://localhost:5001/?culture=no`.

O código a seguir move o delegado de middleware para uma classe:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

A classe de middleware deve incluir:

* Um construtor público com um parâmetro de tipo <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Um método público chamado `Invoke` ou `InvokeAsync`. Esse método precisa:
  * Retornar um `Task`.
  * Aceitar um primeiro parâmetro do tipo <xref:Microsoft.AspNetCore.Http.HttpContext>.
  
Os parâmetros adicionais para o construtor e `Invoke`/`InvokeAsync` são preenchidos pela [DI (injeção de dependência)](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Dependências de middleware

O middleware deve seguir o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) ao expor suas dependências em seu construtor. O middleware é construído uma vez por *tempo de vida do aplicativo*. Confira a seção [Dependências de middleware por solicitação](#per-request-middleware-dependencies) se você precisar compartilhar serviços com middleware em uma solicitação.

Os componentes de middleware podem resolver suas dependências, utilizando a [DI (injeção de dependência)](xref:fundamentals/dependency-injection) por meio de parâmetros do construtor. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) também pode aceitar parâmetros adicionais diretamente.

## <a name="per-request-middleware-dependencies"></a>Dependências de middleware por solicitação

Uma vez que o middleware é construído durante a inicialização do aplicativo, e não por solicitação, os serviços de tempo de vida *com escopo* usados pelos construtores do middleware não são compartilhados com outros tipos de dependência inseridos durante cada solicitação. Se você tiver que compartilhar um serviço *com escopo* entre seu serviço de middleware e serviços de outros tipos, adicione esses serviços à assinatura do método `Invoke`. O método `Invoke` pode aceitar parâmetros adicionais que são preenchidos pela injeção de dependência:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

[As opções de tempo de vida e de registro](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contêm um exemplo completo de middleware com serviços de tempo de vida com *escopo* .

## <a name="middleware-extension-method"></a>Método de extensão de middleware

O seguinte método de extensão expõe o middleware por meio do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

O código a seguir chama o middleware de `Startup.Configure`:

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Recursos adicionais

* [As opções de tempo de vida e de registro](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contêm um exemplo completo de middleware com serviços de tempo de vida com *escopo*, *transitório* e *singleton* .
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
