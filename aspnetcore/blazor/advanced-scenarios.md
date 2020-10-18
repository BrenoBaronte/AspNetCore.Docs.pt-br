---
title: ASP.NET Core Blazor cenários avançados
author: guardrex
description: Saiba mais sobre cenários avançados no Blazor , incluindo como incorporar a lógica de RenderTreeBuilder manual em um aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 295e5dd025afc486be08ecadbf661bf765c2745f
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113602"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>ASP.NET Core Blazor cenários avançados

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

## <a name="no-locblazor-server-circuit-handler"></a>Blazor Server manipulador de circuito

Blazor Server permite que o código defina um *manipulador de circuito*, que permite a execução de código em alterações no estado do circuito de um usuário. Um manipulador de circuito é implementado derivando de `CircuitHandler` e registrando a classe no contêiner de serviço do aplicativo. O exemplo a seguir de um manipulador de circuito rastreia SignalR conexões abertas:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Os manipuladores de circuito são registrados usando DI. Instâncias com escopo são criadas por instância de um circuito. Usando o `TrackingCircuitHandler` no exemplo anterior, um serviço singleton é criado porque o estado de todos os circuitos deve ser acompanhado:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Se os métodos de um manipulador de circuitos personalizados lançarem uma exceção sem tratamento, a exceção será fatal para o Blazor Server circuito. Para tolerar exceções no código de um manipulador ou em métodos chamados, empacote o código em uma ou mais [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruções com tratamento de erros e registro em log.

Quando um circuito termina porque um usuário se desconectou e a estrutura está limpando o estado do circuito, a estrutura descarta o escopo de DI do circuito. Descartar o escopo descarta todos os serviços de DI no escopo do circuito que implementam o <xref:System.IDisposable?displayProperty=fullName> . Se qualquer serviço de DI lançar uma exceção sem tratamento durante a alienação, a estrutura registrará a exceção.

## <a name="manual-rendertreebuilder-logic"></a>Lógica RenderTreeBuilder manual

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes em código C#.

> [!NOTE]
> O uso do <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para criar componentes é um cenário avançado. Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.

Considere o seguinte `PetDetails` componente, que pode ser compilado manualmente em outro componente:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

No exemplo a seguir, o loop no `CreateComponent` método gera três `PetDetails` componentes. Em <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> métodos com um número de sequência, os números de sequência são números de linha de código-fonte. O Blazor algoritmo de diferença depende dos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas. Ao criar um componente com <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> métodos, codifique os argumentos para números de sequência. **O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.** Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent` componente

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Os tipos no <xref:Microsoft.AspNetCore.Components.RenderTree> permitem o processamento dos *resultados* das operações de renderização. Esses são detalhes internos da Blazor implementação da estrutura. Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Números de sequência se relacionam a números de linha de código e não a ordem de execução

Razor os arquivos de componente ( `.razor` ) são sempre compilados. A compilação é uma vantagem potencial sobre a interpretação do código, pois a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.

Um exemplo importante desses aprimoramentos envolve *números de sequência*. Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas. O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.

Considere o seguinte Razor arquivo de componente ( `.razor` ):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

O código anterior é compilado para algo semelhante ao seguinte:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Quando o código é executado pela primeira vez, se `someFlag` for `true` , o Construtor receberá:

| Sequência | Tipo      | Dados   |
| :------: | --------- | :----: |
| 0        | Nó de texto | Primeiro  |
| 1        | Nó de texto | Segundo |

Imagine que `someFlag` se torna `false` e a marcação é renderizada novamente. Desta vez, o Construtor recebe:

| Sequência | Tipo       | Dados   |
| :------: | ---------- | :----: |
| 1        | Nó de texto  | Segundo |

Quando o tempo de execução executa uma comparação, ele vê que o item na sequência `0` foi removido e, portanto, gera o seguinte *script de edição*trivial:

* Remova o primeiro nó de texto.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>O problema com a geração de números de sequência programaticamente

Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Agora, a primeira saída é:

| Sequência | Tipo      | Dados   |
| :------: | --------- | :----: |
| 0        | Nó de texto | Primeiro  |
| 1        | Nó de texto | Segundo |

Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos. `someFlag` está `false` no segundo processamento e a saída é:

| Sequência | Tipo      | Dados   |
| :------: | --------- | ------ |
| 0        | Nó de texto | Segundo |

Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:

* Altere o valor do primeiro nó de texto para `Second` .
* Remova o segundo nó de texto.

A geração de números de sequência perdeu todas as informações úteis sobre onde os `if/else` branches e loops estavam presentes no código original. Isso resulta em uma comparação **duas vezes mais longa** do que antes.

Esse é um exemplo trivial. Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é geralmente mais alto. Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização. Isso geralmente resulta na criação de scripts de edição mais longos porque o algoritmo diff é informado inconscientemente sobre como as estruturas antigas e novas se relacionam entre si.

### <a name="guidance-and-conclusions"></a>Diretrizes e conclusões

* O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.
* A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.
* Não grave blocos longos de lógica implementada manualmente <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> . Prefira `.razor` arquivos e permita que o compilador lide com os números de sequência. Se não for possível evitar lógica manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> , divida blocos longos de código em partes menores encapsuladas em <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> chamadas. Cada região tem seu próprio espaço separado de números de sequência, para que você possa reiniciar de zero (ou qualquer outro número arbitrário) dentro de cada região.
* Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor. O valor inicial e as lacunas são irrelevantes. Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial). 
* Blazor usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam. A comparação é muito mais rápida quando números de sequência são usados e Blazor tem a vantagem de uma etapa de compilação que lida com números de sequência automaticamente para desenvolvedores que criam `.razor` arquivos.
