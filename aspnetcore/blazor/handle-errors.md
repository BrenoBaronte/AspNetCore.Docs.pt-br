---
title: Tratar erros em Blazor aplicativos ASP.NET Core
author: guardrex
description: Descubra como ASP.NET Core Blazor como o Blazor gerencia exceções sem tratamento e como desenvolver aplicativos que detectam e manipulam erros.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: a66075b4c45f2d7767d2ed1950a44a0ba88c7d17
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "83864547"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Tratar erros em Blazor aplicativos ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

Este artigo descreve como o Blazor gerencia exceções sem tratamento e como desenvolver aplicativos que detectam e manipulam erros.

## <a name="detailed-errors-during-development"></a>Erros detalhados durante o desenvolvimento

Quando um Blazor aplicativo não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema. Quando ocorre um erro, os Blazor aplicativos exibem uma barra de ouro na parte inferior da tela:

* Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.
* Em produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.

A interface do usuário para essa experiência de tratamento de erros faz parte dos Blazor modelos de projeto.

Em um Blazor aplicativo Webassembly, personalize a experiência no arquivo *wwwroot/index.html* :

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Em um Blazor aplicativo de servidor, personalize a experiência no arquivo *Pages/_Host. cshtml* :

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

O `blazor-error-ui` elemento é oculto pelos estilos incluídos nos Blazor modelos (*wwwroot/CSS/site. css*) e, em seguida, mostrados quando ocorre um erro:

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a>Como um Blazor aplicativo de servidor reage a exceções sem tratamento

BlazorO servidor é uma estrutura com estado. Enquanto os usuários interagem com um aplicativo, eles mantêm uma conexão com o servidor conhecido como um *circuito*. O circuito contém instâncias de componentes ativos, além de muitos outros aspectos do estado, como:

* A saída renderizada mais recente dos componentes.
* O conjunto atual de delegados de manipulação de eventos que poderia ser disparado por eventos do lado do cliente.

Se um usuário abrir o aplicativo em várias guias do navegador, ele terá vários circuitos independentes.

Blazortrata a maioria das exceções sem tratamento como fatal para o circuito onde elas ocorrem. Se um circuito for encerrado devido a uma exceção sem tratamento, o usuário só poderá continuar a interagir com o aplicativo recarregando a página para criar um novo circuito. Os circuitos fora do que foi encerrado, que são circuitos para outros usuários ou outras guias do navegador, não são afetados. Esse cenário é semelhante a um aplicativo de área de trabalho que falha. O aplicativo com falha deve ser reiniciado, mas outros aplicativos não são afetados.

Um circuito é encerrado quando ocorre uma exceção sem tratamento pelos seguintes motivos:

* Uma exceção sem tratamento geralmente deixa o circuito em um estado indefinido.
* A operação normal do aplicativo não pode ser garantida após uma exceção sem tratamento.
* As vulnerabilidades de segurança podem aparecer no aplicativo se o circuito continuar.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Gerenciar exceções sem tratamento no código do desenvolvedor

Para que um aplicativo continue após um erro, o aplicativo deve ter a lógica de tratamento de erros. As seções posteriores deste artigo descrevem possíveis fontes de exceções sem tratamento.

Em produção, não processe mensagens de exceção de estrutura ou rastreamentos de pilha na interface do usuário. O processamento de mensagens de exceção ou rastreamentos de pilha pode:

* Divulgue informações confidenciais para os usuários finais.
* Ajudar um usuário mal-intencionado a descobrir pontos fracos em um aplicativo que pode comprometer a segurança do aplicativo, do servidor ou da rede.

## <a name="log-errors-with-a-persistent-provider"></a>Registrar erros em log com um provedor persistente

Se ocorrer uma exceção sem tratamento, a exceção será registrada em <xref:Microsoft.Extensions.Logging.ILogger> instâncias configuradas no contêiner de serviço. Por padrão, Blazor os aplicativos registram em log a saída do console com o provedor de log do console. Considere fazer logon em um local mais permanente com um provedor que gerencia o tamanho do log e a rotação do log. Para obter mais informações, consulte <xref:fundamentals/logging/index>.

Durante o desenvolvimento, Blazor o geralmente envia os detalhes completos das exceções para o console do navegador para auxiliar na depuração. Na produção, os erros detalhados no console do navegador são desabilitados por padrão, o que significa que os erros não são enviados aos clientes, mas os detalhes completos da exceção ainda são registrados no lado do servidor. Para obter mais informações, consulte <xref:fundamentals/error-handling>.

Você deve decidir quais incidentes registrar e o nível de gravidade dos incidentes registrados. Usuários hostis podem ser capazes de disparar erros deliberadamente. Por exemplo, não registre um incidente de um erro em que um desconhecido `ProductId` é fornecido na URL de um componente que exibe detalhes do produto. Nem todos os erros devem ser tratados como incidentes de alta severidade para registro em log.

Para obter mais informações, consulte <xref:fundamentals/logging/index#create-logs-in-blazor>.

## <a name="places-where-errors-may-occur"></a>Locais onde podem ocorrer erros

O Framework e o código do aplicativo podem disparar exceções sem tratamento em qualquer um dos seguintes locais:

* [Instanciação de componente](#component-instantiation)
* [Métodos de ciclo de vida](#lifecycle-methods)
* [Lógica de renderização](#rendering-logic)
* [Manipuladores de eventos](#event-handlers)
* [Disposição do componente](#component-disposal)
* [Interoperabilidade do JavaScript](#javascript-interop)
* [BlazorReprocessamento de servidor](#blazor-server-prerendering)

As exceções sem tratamento anteriores são descritas nas seções a seguir deste artigo.

### <a name="component-instantiation"></a>Instanciação de componente

Quando o Blazor cria uma instância de um componente:

* O construtor do componente é invocado.
* Os construtores de quaisquer serviços não singleton de DI fornecidos ao construtor do componente por meio da [`@inject`](xref:mvc/views/razor#inject) diretiva ou do [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) atributo são invocados.

Um Blazor circuito de servidor falha quando qualquer Construtor executado ou um setter para qualquer `[Inject]` propriedade gera uma exceção sem tratamento. A exceção é fatal porque a estrutura não pode instanciar o componente. Se a lógica do Construtor puder gerar exceções, o aplicativo deverá interceptar as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.

### <a name="lifecycle-methods"></a>Métodos de ciclo de vida

Durante o tempo de vida de um componente, Blazor o invoca os seguintes [métodos de ciclo de vida](xref:blazor/lifecycle):

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

Se qualquer método de ciclo de vida lançar uma exceção, de forma síncrona ou assíncrona, a exceção será fatal para um Blazor circuito de servidor. Para componentes que lidam com erros em métodos de ciclo de vida, adicione a lógica de tratamento de erros.

No exemplo a seguir, onde <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> chama um método para obter um produto:

* Uma exceção gerada no `ProductRepository.GetProductByIdAsync` método é manipulada por uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .
* Quando o `catch` bloco é executado:
  * `loadFailed`é definido como `true` , que é usado para exibir uma mensagem de erro para o usuário.
  * O erro é registrado.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Lógica de renderização

A marcação declarativa em um `.razor` arquivo de componente é compilada em um método C# chamado <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> . Quando um componente renderiza, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> o executa e cria uma estrutura de dados que descreve os elementos, o texto e os componentes filho do componente renderizado.

A lógica de renderização pode gerar uma exceção. Um exemplo desse cenário ocorre quando o `@someObject.PropertyName` é avaliado, mas `@someObject` é `null` . Uma exceção sem tratamento gerada pela lógica de renderização é fatal para um Blazor circuito de servidor.

Para evitar uma exceção de referência nula na lógica de renderização, verifique se há um `null` objeto antes de acessar seus membros. No exemplo a seguir, `person.Address` as propriedades não são acessadas se `person.Address` for `null` :

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

O código anterior pressupõe que `person` não é `null` . Geralmente, a estrutura do código garante que um objeto exista no momento em que o componente é renderizado. Nesses casos, não é necessário verificar `null` na lógica de renderização. No exemplo anterior, `person` talvez seja garantido que exista porque `person` é criado quando o componente é instanciado.

### <a name="event-handlers"></a>Manipuladores de eventos

O código do lado do cliente dispara invocações de código C# quando manipuladores de eventos são criados usando:

* `@onclick`
* `@onchange`
* Outros `@on...` atributos
* `@bind`

O código do manipulador de eventos pode gerar uma exceção sem tratamento nesses cenários.

Se um manipulador de eventos lançar uma exceção sem tratamento (por exemplo, uma consulta de banco de dados falhar), a exceção será fatal para um Blazor circuito de servidor. Se o aplicativo chamar o código que poderia falhar por motivos externos, intercepte as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.

Se o código do usuário não interceptar e manipular a exceção, a estrutura registrará a exceção e encerrará o circuito.

### <a name="component-disposal"></a>Disposição do componente

Um componente pode ser removido da interface do usuário, por exemplo, porque o usuário navegou para outra página. Quando um componente que implementa o <xref:System.IDisposable?displayProperty=fullName> é removido da interface do usuário, a estrutura chama o <xref:System.IDisposable.Dispose%2A> método do componente.

Se o método do componente `Dispose` lançar uma exceção sem tratamento, a exceção será fatal para um Blazor circuito de servidor. Se a lógica de descarte puder gerar exceções, o aplicativo deverá interceptar as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com o tratamento de erros e o registro em log.

Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable> .

### <a name="javascript-interop"></a>Interoperabilidade do JavaScript

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>permite que o código .NET faça chamadas assíncronas para o tempo de execução do JavaScript no navegador do usuário.

As seguintes condições se aplicam ao tratamento de erros com <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :

* Se uma chamada para <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> falhar de forma síncrona, ocorrerá uma exceção do .net. Uma chamada para <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> pode falhar, por exemplo, porque os argumentos fornecidos não podem ser serializados. O código do desenvolvedor deve capturar a exceção. Se o código do aplicativo em um manipulador de eventos ou método de ciclo de vida de componente não tratar uma exceção, a exceção resultante será fatal para um Blazor circuito de servidor.
* Se uma chamada para <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> falhar de forma assíncrona, o .NET <xref:System.Threading.Tasks.Task> falhará. Uma chamada para <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> pode falhar, por exemplo, porque o código do lado do JavaScript gera uma exceção ou retorna um `Promise` que foi concluído como `rejected` . O código do desenvolvedor deve capturar a exceção. Se estiver usando o operador [Await](/dotnet/csharp/language-reference/keywords/await) , considere encapsular a chamada de método em uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log. Caso contrário, o código com falha resultará em uma exceção sem tratamento que é fatal para um Blazor circuito de servidor.
* Por padrão, as chamadas para <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> devem ser concluídas em um determinado período ou a chamada atinge o tempo limite. O período de tempo limite padrão é de um minuto. O tempo limite protege o código contra uma perda na conectividade de rede ou no código JavaScript que nunca envia uma mensagem de conclusão. Se a chamada atingir o tempo limite, o resultado <xref:System.Threading.Tasks> falhará com um <xref:System.OperationCanceledException> . Interceptar e processar a exceção com registro em log.

Da mesma forma, o código JavaScript pode iniciar chamadas para métodos do .NET indicados pelo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo] (xref: mais alto-do que/. de chamada-dotNet-de-JavaScript). Se esses métodos .NET lançarem uma exceção sem tratamento:

* A exceção não é tratada como fatal para um Blazor circuito de servidor.
* O lado do JavaScript `Promise` é rejeitado.

Você tem a opção de usar o código de tratamento de erros no lado do .NET ou no lado do JavaScript da chamada do método.

Para obter mais informações, consulte os seguintes artigos:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>BlazorPré-processamento de servidor

Blazoros componentes podem ser renderizados usando o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para que sua marcação HTML renderizada seja retornada como parte da solicitação HTTP inicial do usuário. Isso funciona por:

* Criar um novo circuito para todos os componentes renderizados que fazem parte da mesma página.
* Gerando o HTML inicial.
* Tratar o circuito como `disconnected` até que o navegador do usuário estabeleça uma SignalR conexão de volta para o mesmo servidor. Quando a conexão é estabelecida, a interatividade no circuito é retomada e a marcação HTML dos componentes é atualizada.

Se qualquer componente lançar uma exceção sem tratamento durante o pré-processamento, por exemplo, durante um método de ciclo de vida ou na lógica de renderização:

* A exceção é fatal para o circuito.
* A exceção é gerada na pilha de chamadas a partir do <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> auxiliar de marca. Portanto, toda a solicitação HTTP falha, a menos que a exceção seja explicitamente detectada pelo código do desenvolvedor.

Em circunstâncias normais, quando o pré-processamento falha, continuar a criar e renderizar o componente não faz sentido porque um componente de trabalho não pode ser renderizado.

Para tolerar erros que podem ocorrer durante o pré-processamento, a lógica de tratamento de erro deve ser colocada dentro de um componente que pode gerar exceções. Use instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log. Em vez de encapsular o <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> auxiliar de marca em uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) , coloque a lógica de tratamento de erro no componente renderizado pelo <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> auxiliar de marca.

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="recursive-rendering"></a>Renderização recursiva

Os componentes podem ser aninhados recursivamente. Isso é útil para representar estruturas de dados recursivas. Por exemplo, um `TreeNode` componente pode renderizar mais `TreeNode` componentes para cada um dos filhos do nó.

Ao renderizar recursivamente, evite codificar padrões que resultam em recursão infinita:

* Não renderizar recursivamente uma estrutura de dados que contém um ciclo. Por exemplo, não renderize um nó de árvore cujos filhos incluam a si mesmo.
* Não crie uma cadeia de layouts que contenham um ciclo. Por exemplo, não crie um layout cujo layout seja próprio.
* Não permita que um usuário final viole invariáveis de recursão (regras) por meio de entradas de dados mal-intencionados ou chamadas de interoperabilidade do JavaScript.

Loops infinitos durante a renderização:

* Faz com que o processo de renderização continue para sempre.
* É equivalente a criar um loop não finalizado.

Nesses cenários, um circuito de Blazor servidor afetado falha e o thread geralmente tenta:

* Consuma tanto tempo de CPU quanto permitido pelo sistema operacional, indefinidamente.
* Consuma uma quantidade ilimitada de memória do servidor. O consumo de memória ilimitada é equivalente ao cenário em que um loop não finalizado adiciona entradas a uma coleção em cada iteração.

Para evitar padrões de recursão infinitos, verifique se o código de renderização recursivo contém condições de parada adequadas.

### <a name="custom-render-tree-logic"></a>Lógica de árvore de renderização personalizada

A maioria dos Blazor componentes são implementados como arquivos *. Razor* e são compilados para produzir lógica que opera em um <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para renderizar sua saída. Um desenvolvedor pode implementar A <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> lógica manualmente usando o código C# de procedimento. Para obter mais informações, consulte <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> O uso da lógica do construtor de árvore de renderização manual é considerado um cenário avançado e não seguro, não recomendado para o desenvolvimento geral de componentes.

Se <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> o código for escrito, o desenvolvedor deverá garantir a exatidão do código. Por exemplo, o desenvolvedor deve garantir que:

* Chamadas para <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> e <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> são balanceadas corretamente.
* Os atributos são adicionados somente nos locais corretos.

A lógica do construtor de árvore de renderização manual incorreta pode causar um comportamento indefinido arbitrário, incluindo falhas, travamentos do servidor e vulnerabilidades de segurança.

Considere a lógica do construtor de árvore de renderização manual no mesmo nível de complexidade e com o mesmo nível de *perigo* de escrever código de assembly ou instruções MSIL manualmente.
