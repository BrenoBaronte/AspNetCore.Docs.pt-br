---
title: Chamar métodos .NET de funções JavaScript no ASP.NET Core Blazor
author: guardrex
description: Saiba como invocar métodos .NET de funções JavaScript em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e602f29e6932280f4625ade64201ff232e02150d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751627"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a>Chamar métodos .NET de funções JavaScript no ASP.NET Core Blazor

Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)

Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript. Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).

Este artigo aborda a invocação de métodos .NET do JavaScript. Para obter informações sobre como chamar funções JavaScript do .NET, consulte <xref:blazor/call-javascript-from-dotnet> .

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

> [!NOTE]
> Adicione arquivos JS ( `<script>` marcas) antes da marca de fechamento `</body>` no `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ). Verifique se os arquivos JS com métodos de interoperabilidade JS estão incluídos antes dos Blazor arquivos do Framework js.

## <a name="static-net-method-call"></a>Chamada de método .NET estático

Para invocar um método .NET estático do JavaScript, use `DotNet.invokeMethod` as `DotNet.invokeMethodAsync` funções ou. Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos. A versão assíncrona é preferida para dar suporte a Blazor Server cenários. O método .NET deve ser público, estático e ter o [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo. Atualmente, não há suporte para chamar métodos genéricos abertos.

O aplicativo de exemplo inclui um método C# para retornar uma `int` matriz. O [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo é aplicado ao método.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

O JavaScript servido para o cliente invoca o método .NET do C#.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Quando o **`Trigger .NET static method ReturnArrayAsync`** botão estiver selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.

A saída do console é:

```console
Array(4) [ 1, 2, 3, 4 ]
```

O quarto valor de matriz é enviado por push para a matriz ( `data.push(4);` ) retornada por `ReturnArrayAsync` .

Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente usando o [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) Construtor de atributos:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

No arquivo JavaScript do lado do cliente:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

## <a name="instance-method-call"></a>Chamada de método de instância

Você também pode chamar os métodos de instância do .NET do JavaScript. Para invocar um método de instância .NET do JavaScript:

* Passe a instância do .NET por referência ao JavaScript:
  * Faça uma chamada estática para <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .
  * Empacote a instância em uma <xref:Microsoft.JSInterop.DotNetObjectReference> instância e chame <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> na <xref:Microsoft.JSInterop.DotNetObjectReference> instância. Descarte de <xref:Microsoft.JSInterop.DotNetObjectReference> objetos (um exemplo aparece mais adiante nesta seção).
* Invoque os métodos de instância do .NET na instância usando as `invokeMethod` `invokeMethodAsync` funções ou. A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.

> [!NOTE]
> O aplicativo de exemplo registra as mensagens no console do lado do cliente. Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.

Quando o **`Trigger .NET instance method HelloHelper.SayHello`** botão é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um nome, `Blazor` , para o método.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` Invoca a função JavaScript `sayHello` com uma nova instância do `HelloHelper` .

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

O nome é passado para o `HelloHelper` Construtor de, que define a `HelloHelper.Name` propriedade. Quando a função JavaScript `sayHello` é executada, `HelloHelper.SayHello` retorna a `Hello, {Name}!` mensagem, que é gravada no console pela função JavaScript.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Saída do console nas ferramentas de desenvolvedor da Web do navegador:

```console
Hello, Blazor!
```

Para evitar um vazamento de memória e permitir a coleta de lixo em um componente que cria um <xref:Microsoft.JSInterop.DotNetObjectReference> , adote uma das seguintes abordagens:

* Descarte o objeto na classe que criou a <xref:Microsoft.JSInterop.DotNetObjectReference> instância:

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  O padrão anterior mostrado na `ExampleJsInterop` classe também pode ser implementado em um componente:

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

* Quando o componente ou classe não descartar o <xref:Microsoft.JSInterop.DotNetObjectReference> , descarte o objeto no cliente chamando `.dispose()` :

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Chamada de método de instância de componente

Para invocar os métodos .NET de um componente:

* Use a `invokeMethod` `invokeMethodAsync` função ou para fazer uma chamada de método estático para o componente.
* O método estático do componente encapsula a chamada para seu método de instância como uma chamada <xref:System.Action> .

> [!NOTE]
> Para Blazor Server aplicativos, em que vários usuários podem estar usando simultaneamente o mesmo componente, use uma classe auxiliar para invocar métodos de instância.
>
> Para obter mais informações, consulte a seção [classe do método da instância do componente](#component-instance-method-helper-class) .

No JavaScript do lado do cliente:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

`Pages/JSInteropComponent.razor`:

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

Para passar argumentos para o método de instância:

* Adicione parâmetros à invocação do método JS. No exemplo a seguir, um nome é passado para o método. Parâmetros adicionais podem ser adicionados à lista, conforme necessário.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

* Forneça os tipos corretos para os <xref:System.Action> parâmetros. Forneça a lista de parâmetros para os métodos C#. Invoque o <xref:System.Action> ( `UpdateMessage` ) com os parâmetros ( `action.Invoke(name)` ).

  `Pages/JSInteropComponent.razor`:

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  Saída `message` quando o botão do **método de chamada js** for selecionado:

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>Classe auxiliar de método de instância de componente

A classe auxiliar é usada para invocar um método de instância como um <xref:System.Action> . As classes auxiliares são úteis quando:

* Vários componentes do mesmo tipo são renderizados na mesma página.
* Um Blazor Server aplicativo é usado, onde vários usuários podem estar usando um componente simultaneamente.

No exemplo a seguir:

* O `JSInteropExample` componente contém vários `ListItem` componentes.
* Cada `ListItem` componente é composto por uma mensagem e um botão.
* Quando um `ListItem` botão de componente é selecionado, `ListItem` esse `UpdateMessage` método altera o texto do item da lista e oculta o botão.

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

No JavaScript do lado do cliente:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).

`Shared/ListItem.razor`:

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

`Pages/JSInteropExample.razor`:

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evitar referências circulares de objeto

Os objetos que contêm referências circulares não podem ser serializados no cliente para:

* Chamadas de método .NET.
* O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.

Para obter mais informações, consulte os seguintes problemas:

* [Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a>Limites de tamanho em chamadas de interoperabilidade JS

No Blazor WebAssembly , a estrutura não impõe um limite no tamanho das entradas e saídas de interoperabilidade do js.

No Blazor Server , as chamadas de interoperabilidade js têm tamanho limitado pelo SignalR tamanho máximo de mensagem de entrada permitido para métodos de Hub, que é imposto por <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (padrão: 32 KB). JS para SignalR mensagens do .net maiores do que <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> lançar um erro. A estrutura não impõe um limite no tamanho de uma SignalR mensagem do hub para um cliente.

Quando SignalR o log não está definido como [debug](xref:Microsoft.Extensions.Logging.LogLevel) ou [trace](xref:Microsoft.Extensions.Logging.LogLevel), um erro de tamanho de mensagem aparece apenas no console de ferramentas de desenvolvimento do navegador:

> Erro: conexão desconectada com erro ' erro: o servidor retornou um erro ao fechar: a conexão foi encerrada com um erro. '.

Quando o [ SignalR registro em log do lado do servidor](xref:signalr/diagnostics#server-side-logging) é definido como [depuração](xref:Microsoft.Extensions.Logging.LogLevel) ou [rastreamento](xref:Microsoft.Extensions.Logging.LogLevel), o registro em log do lado do servidor é um <xref:System.IO.InvalidDataException> erro de tamanho de mensagem.

`appsettings.Development.json`:

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> System. IO. InvalidDataException: o tamanho máximo da mensagem de 32768B foi excedido. O tamanho da mensagem pode ser configurado em AddHubOptions.

Aumente o limite definindo <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> em `Startup.ConfigureServices` . O exemplo a seguir define o tamanho máximo da mensagem de recebimento como 64 KB (64 * 1024):

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

Aumentar o SignalR limite de tamanho da mensagem de entrada é o custo de exigir mais recursos do servidor e expõe o servidor para aumentar os riscos de um usuário mal-intencionado. Além disso, a leitura de uma grande quantidade de conteúdo na memória como cadeias de caracteres ou matrizes de bytes também pode resultar em alocações que funcionam mal com o coletor de lixo, resultando em penalidades de desempenho adicionais.

Uma opção para ler grandes cargas é enviar o conteúdo em partes menores e processar a carga como um <xref:System.IO.Stream> . Isso pode ser usado ao ler cargas JSON grandes ou se os dados estiverem disponíveis em JavaScript como bytes brutos. Para obter um exemplo que demonstra o envio de grandes cargas binárias no Blazor Server que usa técnicas semelhantes ao `InputFile` componente, consulte o [aplicativo de exemplo enviar binário](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

Considere as seguintes diretrizes ao desenvolver código que transfere uma grande quantidade de dados entre o JavaScript e o Blazor :

* Divida os dados em partes menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.
* Não aloque objetos grandes em JavaScript e código C#.
* Não bloqueie o thread da interface do usuário principal por longos períodos ao enviar ou receber dados.
* Libere qualquer memória consumida quando o processo for concluído ou cancelado.
* Impor os seguintes requisitos adicionais para fins de segurança:
  * Declare o tamanho máximo de arquivo ou de dados que pode ser passado.
  * Declare a taxa de carregamento mínima do cliente para o servidor.
* Depois que os dados são recebidos pelo servidor, os dados podem ser:
  * Temporariamente armazenados em um buffer de memória até que todos os segmentos sejam coletados.
  * Consumido imediatamente. Por exemplo, os dados podem ser armazenados imediatamente em um banco de dado ou gravados no disco à medida que cada segmento é recebido.

## <a name="js-modules"></a>Módulos JS

Para isolamento de JS, o JS Interop funciona com o suporte padrão do navegador para [módulos ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificação ECMAScript](https://tc39.es/ecma262/#sec-modules)).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/call-javascript-from-dotnet>
* [`InteropComponent.razor` exemplo (dotNet/AspNetCore do repositório GitHub, Branch de lançamento 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
