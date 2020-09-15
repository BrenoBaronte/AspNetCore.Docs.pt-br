---
title: Blazor WebAssemblyPráticas recomendadas de desempenho de ASP.NET Core
author: pranavkm
description: Dicas para aumentar o desempenho em Blazor WebAssembly aplicativos ASP.NET Core e evitar problemas comuns de desempenho.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 5d3cd1480dd37f437b2d6d5a89af0a842286be95
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080258"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Blazor WebAssemblyPráticas recomendadas de desempenho de ASP.NET Core

Por [Pranav Krishnamoorthy](https://github.com/pranavkm)

Este artigo fornece diretrizes para ASP.NET Core Blazor WebAssembly práticas recomendadas de desempenho.

## <a name="avoid-unnecessary-component-renders"></a>Evitar renderizações de componente desnecessárias

Blazoro algoritmo de diferenciação do evita a rerenderização de um componente quando o algoritmo percebe que o componente não foi alterado. Substitua o <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> controle refinado sobre a renderização do componente.

Se criar um componente somente de interface do usuário que nunca é alterado após a renderização inicial, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para retornar `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

A maioria dos aplicativos não requer controle refinado, mas <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pode ser usada para renderizar seletivamente um componente que responde a um evento de interface do usuário. Usar <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> também pode ser importante em cenários em que um grande número de componentes é renderizado. Considere uma grade, em que o uso de <xref:Microsoft.AspNetCore.Components.EventCallback> em um componente em uma célula da grade é chamado <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> na grade. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>A chamada causa um novo processamento de cada componente filho. Se apenas um pequeno número de células exigir rerenderização, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para evitar a penalidade de desempenho de renderizações desnecessárias.

No exemplo a seguir:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> é substituído e definido como o valor do <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> campo, que é inicialmente `false` quando o componente é carregado.
* Quando o botão é selecionado, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> é definido como `true` , o que força o componente a renderizar novamente com o atualizado `currentCount` .
* Imediatamente após o reprocessamento, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> o define o valor de <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> voltar para `false` para evitar a rerenderização posterior até a próxima vez que o botão for selecionado.

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

Para obter mais informações, consulte <xref:blazor/components/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Virtualizar fragmentos reutilizáveis

Os componentes oferecem uma abordagem conveniente para produzir fragmentos reutilizáveis de código e marcação. Em geral, é recomendável criar componentes individuais que melhor se alinhem com os requisitos do aplicativo. Uma limitação é que cada componente filho adicional contribui para o tempo total que leva para renderizar um componente pai. Para a maioria dos aplicativos, a sobrecarga adicional é insignificante. Os aplicativos que produzem um grande número de componentes devem considerar o uso de estratégias para reduzir a sobrecarga de processamento, como limitar o número de componentes renderizados.

Por exemplo, uma grade ou lista que renderiza centenas de linhas que contêm componentes tem uso intensivo de processador para renderização. Considere a possibilidade de virtualizar um layout de grade ou lista para que apenas um subconjunto dos componentes seja renderizado em um determinado momento. Para obter um exemplo de renderização de subconjunto de componentes, consulte os seguintes componentes no [ `Virtualization` aplicativo de exemplo (repositório GitHub ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): um componente escrito em C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para renderizar um conjunto de linhas de dados meteorológicos com base na rolagem do usuário.
* `FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): usa o `Virtualize` componente para exibir 25 linhas de dados meteorológicos por vez.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Evitar a interoperabilidade JavaScript para empacotar dados

No Blazor WebAssembly , uma chamada de interoperabilidade JavaScript (js) deve atravessar o limite Webassembly-js. A serialização e a desserialização de conteúdo entre os dois contextos cria sobrecarga de processamento para o aplicativo. As chamadas de interoperabilidade do JS frequentes muitas vezes afetam o desempenho. Para reduzir o marshaling dos dados em todo o limite, determine se o aplicativo pode consolidar muitas cargas pequenas em uma única carga grande para evitar o alto volume de alternância de contexto entre Webassembly e JS.

## <a name="use-systemtextjson"></a>Usar System.Text.Jsem

Blazora implementação de interoperabilidade do JS depende de <xref:System.Text.Json> , que é uma biblioteca de SERIALIZAÇÃO JSON de alto desempenho com alocação de memória insuficiente. O uso <xref:System.Text.Json> do não resulta em tamanho de carga de aplicativo adicional ao adicionar uma ou mais bibliotecas JSON alternativas.

Para obter diretrizes de migração, consulte [como migrar do `Newtonsoft.Json` para `System.Text.Json` o ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Usar APIs de interoperabilidade do JS síncronas e não marshalled, quando apropriado

Blazor WebAssembly o oferece duas versões adicionais do <xref:Microsoft.JSInterop.IJSRuntime> sobre a única versão disponível para Blazor Server aplicativos:

* <xref:Microsoft.JSInterop.IJSInProcessRuntime> permite invocar chamadas de interoperabilidade JS de forma síncrona, que tem menos sobrecarga do que as versões assíncronas:

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permite chamadas de interoperabilidade JS não marshalled:

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > Embora o uso do <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> tenha a menor sobrecarga das abordagens de interoperabilidade do js, as APIs de JavaScript necessárias para interagir com essas APIs não estão documentadas no momento e estão sujeitas a alterações significativas em versões futuras.

## <a name="reduce-app-size"></a>Reduzir o tamanho do aplicativo

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>Corte de IL (linguagem intermediária)

A [corte de assemblies não utilizados de um Blazor WebAssembly aplicativo](xref:blazor/host-and-deploy/configure-trimmer) reduz o tamanho do aplicativo removendo o código não utilizado nos binários do aplicativo. Por padrão, o corte é executado durante a publicação de um aplicativo. Para se beneficiar da remoção, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Vinculação de IL (linguagem intermediária)

A [vinculação de um Blazor WebAssembly aplicativo](xref:blazor/host-and-deploy/configure-linker) reduz o tamanho do aplicativo ao cortar o código não utilizado nos binários do aplicativo. Por padrão, o vinculador de linguagem intermediária (IL) só é habilitado durante a criação da `Release` configuração. Para se beneficiar disso, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a>Assemblies de carga lenta

Carregar assemblies em tempo de execução quando os assemblies são exigidos por uma rota. Para obter mais informações, consulte <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Compactação

Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução. Blazor depende do servidor para executar negotation de conteúdo e fornecer arquivos compactados estaticamente.

Depois que um aplicativo for implantado, verifique se o aplicativo serve arquivos compactados. Inspecione a guia rede no Ferramentas para Desenvolvedores de um navegador e verifique se os arquivos são servidos com `Content-Encoding: br` ou `Content-Encoding: gz` . Se o host não estiver servindo arquivos compactados, siga as instruções em <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Desabilitar recursos não utilizados

Blazor WebAssemblyo tempo de execução do inclui os seguintes recursos do .NET que podem ser desabilitados se o aplicativo não exigir um tamanho de carga menor:

* Um arquivo de dados está incluído para tornar as informações de fuso horário corretas. Se o aplicativo não exigir esse recurso, considere a possibilidade de desabilitá-lo definindo a `BlazorEnableTimeZoneSupport` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Por padrão, Blazor WebAssembly o transporta os recursos de globalização necessários para exibir valores, como datas e moedas, na cultura do usuário. Se o aplicativo não exigir localização, você poderá [Configurar o aplicativo para dar suporte à cultura invariável](xref:blazor/globalization-localization), que se baseia na `en-US` cultura:

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* As informações de agrupamento estão incluídas para fazer APIs como o <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionamento correto. Se você tiver certeza de que o aplicativo não exige os dados de agrupamento, considere desabilitá-lo definindo a `BlazorWebAssemblyPreserveCollationData` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
