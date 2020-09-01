---
title: referência de sintaxe do Razor para ASP.NET Core
author: rick-anderson
description: Saiba mais sobre Razor a sintaxe de marcação para inserir código baseado em servidor em páginas da Web.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: 9c2bbd2d463af8a2ea7db716d01bf1436338ea77
ms.sourcegitcommit: cd861463faf44956855e3c4b3669483bbc4a7463
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89101355"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a>Razor referência de sintaxe para ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)e [Dan Vicarel](https://github.com/Rabadash8820)

Razor é uma sintaxe de marcação para inserir código baseado em servidor em páginas da Web. A Razor sintaxe consiste em Razor marcação, C# e HTML. Os arquivos que contêm Razor geralmente têm uma extensão de arquivo *. cshtml* . Razortambém é encontrado em arquivos de [ Razor componentes](xref:blazor/components/index) (*. Razor*).

## <a name="rendering-html"></a>Renderização de HTML

O Razor idioma padrão é HTML. O processamento de HTML da Razor marcação não é diferente do processamento de HTML de um arquivo HTML. A marcação HTML em arquivos *. cshtml* Razor é renderizada pelo servidor inalterado.

## <a name="no-locrazor-syntax"></a>Sintaxe de Razor

Razor dá suporte a C# e usa o `@` símbolo para fazer a transição de HTML para C#. Razor avalia as expressões C# e as renderiza na saída HTML.

Quando um `@` símbolo é seguido por uma [ Razor palavra-chave reservada](#razor-reserved-keywords), ele faz a transição para uma Razor marcação específica. Caso contrário, ele faz a transição para C# simples.

Para escapar de um `@` símbolo na Razor marcação, use um segundo `@` símbolo:

```cshtml
<p>@@Username</p>
```

O código é renderizado em HTML com um único símbolo `@`:

```html
<p>@Username</p>
```

Conteúdo e atributos HTML que contêm endereços de email não tratam o símbolo `@` como um caractere de transição. Os endereços de email no exemplo a seguir são inalterados pela Razor análise:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a>Expressões implícitas Razor

As Razor expressões implícitas começam com `@` seguido pelo código C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Com exceção da palavra-chave C# `await`, expressões implícitas não devem conter espaços. Se a instrução C# tiver uma terminação clara, espaços podem ser misturados:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Expressões implícitas **não podem** conter elementos genéricos de C#, pois caracteres dentro de colchetes (`<>`) são interpretados como uma marca HTML. O código a seguir é **inválido**:

```cshtml
<p>@GenericMethod<int>()</p>
```

O código anterior gera um erro de compilador semelhante a um dos seguintes:

* O elemento "int" não foi fechado. Todos os elementos devem ter fechamento automático ou ter uma marca de fim correspondente.
* Não é possível converter o grupo de métodos "GenericMethod" em um "object" de tipo não delegado. Você pretendia invocar o método?

Chamadas de método genérico devem ser encapsuladas em uma [ Razor expressão explícita](#explicit-razor-expressions) ou em um [ Razor bloco de código](#razor-code-blocks).

## <a name="explicit-no-locrazor-expressions"></a>Expressões explícitas Razor

Expressões explícitas Razor consistem em um `@` símbolo com parênteses equilibrados. Para renderizar a hora da semana passada, a seguinte Razor marcação é usada:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Qualquer conteúdo dentro dos parênteses `@()` é avaliado e renderizado para a saída.

Expressões implícitas, descritas na seção anterior, geralmente não podem conter espaços. No código a seguir, uma semana não é subtraída da hora atual:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

O código renderiza o HTML a seguir:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Expressões explícitas podem ser usadas para concatenar texto com um resultado de expressão:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Sem a expressão explícita, `<p>Age@joe.Age</p>` é tratado como um endereço de email e `<p>Age@joe.Age</p>` é renderizado. Quando escrito como uma expressão explícita, `<p>Age33</p>` é renderizado.

Expressões explícitas podem ser usadas para renderizar a saída de métodos genéricos em arquivos *.cshtml*. A marcação a seguir mostra como corrigir o erro mostrado anteriormente causado pelos colchetes de um C# genérico. O código é escrito como uma expressão explícita:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Codificação de expressão

Expressões em C# que são avaliadas como uma cadeia de caracteres estão codificadas em HTML. Expressões em C# que são avaliadas como `IHtmlContent` são renderizadas diretamente por meio `IHtmlContent.WriteTo`. Expressões em C# que não são avaliadas como `IHtmlContent` são convertidas em uma cadeia de caracteres por `ToString` e codificadas antes que sejam renderizadas.

```cshtml
@("<span>Hello World</span>")
```

O código anterior processa o seguinte HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

O HTML é mostrado no navegador como texto sem formatação:

&lt;&gt;Olá, mundo &lt; /span de span&gt;

A saída `HtmlHelper.Raw` não é codificada, mas renderizada como marcação HTML.

> [!WARNING]
> Usar `HtmlHelper.Raw` em uma entrada do usuário que não está limpa é um risco de segurança. A entrada do usuário pode conter JavaScript mal-intencionado ou outras formas de exploração. Limpar a entrada do usuário é difícil. Evite usar `HtmlHelper.Raw` com a entrada do usuário.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

O código renderiza o HTML a seguir:

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a>Razor blocos de código

Razor os blocos de código começam com `@` e são incluídos por `{}` . Diferente das expressões, o código C# dentro de blocos de código não é renderizado. Blocos de código e expressões em uma exibição compartilham o mesmo escopo e são definidos em ordem:

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

O código renderiza o HTML a seguir:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

Em blocos de código, declare [funções locais](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) com uma marcação para servir como métodos de modelagem:

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

O código renderiza o HTML a seguir:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Transições implícitas

O idioma padrão em um bloco de código é C#, mas a Razor página pode fazer a transição de volta para o HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Transição delimitada explícita

Para definir uma subseção de um bloco de código que deve renderizar HTML, coloque os caracteres para renderização com a Razor `<text>` marca:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Use essa abordagem para renderizar HTML que não está circundado por uma marca HTML. Sem um HTML ou Razor marca, Razor ocorre um erro de tempo de execução.

A marca `<text>` é útil para controlar o espaço em branco ao renderizar conteúdo:

* Somente o conteúdo entre a marca `<text>` é renderizado.
* Não aparece nenhum espaço em branco antes ou depois da marca `<text>` na saída HTML.

### <a name="explicit-line-transition"></a>Transição de linha explícita

Para renderizar o restante de uma linha inteira como HTML dentro de um bloco de código, use a `@:` sintaxe:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Sem o `@:` no código, um Razor erro de tempo de execução é gerado.

`@`Caracteres extras em um Razor arquivo podem causar erros de compilador em instruções posteriores no bloco. Esses erros do compilador podem ser difíceis de entender porque o erro real ocorre antes do erro relatado. Esse erro é comum após combinar várias expressões implícitas/explícitas em um bloco de código único.

## <a name="control-structures"></a>Estruturas de controle

Estruturas de controle são uma extensão dos blocos de código. Todos os aspectos dos blocos de código (transição para marcação, C# embutido) também se aplicam às seguintes estruturas:

### <a name="conditionals-if-else-if-else-and-switch"></a>Condicionais `@if, else if, else, and @switch`

`@if` controla quando o código é executado:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` e `else if` não exigem o símbolo `@`:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

A marcação a seguir mostra como usar uma instrução switch:

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Loop `@for, @foreach, @while, and @do while`

O HTML no modelo pode ser renderizado com instruções de controle em loop. Para renderizar uma lista de pessoas:

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Há suporte para as seguintes instruções em loop:

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>`@using` composto

Em C#, uma instrução `using` é usada para garantir que um objeto seja descartado. No Razor , o mesmo mecanismo é usado para criar auxiliares HTML que contêm conteúdo adicional. No código a seguir, os Auxiliares HTML renderizam uma marca `<form>` com a instrução `@using`:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

O tratamento de exceções é semelhante ao de C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor o tem a capacidade de proteger seções críticas com instruções Lock:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Comentários

Razor dá suporte a comentários em C# e HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

O código renderiza o HTML a seguir:

```html
<!-- HTML comment -->
```

Razor Os comentários são removidos pelo servidor antes que a página da Web seja renderizada. Razor usa `@*  *@` para delimitar comentários. O código a seguir é comentado, de modo que o servidor não renderiza nenhuma marcação:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Diretivas

Razor as diretivas são representadas por expressões implícitas com palavras-chave reservadas após o `@` símbolo. Uma diretiva geralmente altera o modo como uma exibição é analisada ou habilita uma funcionalidade diferente.

Entender como o Razor gera código para uma exibição torna mais fácil entender como as diretivas funcionam.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

O código gera uma classe semelhante à seguinte:

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

Posteriormente neste artigo, a seção [inspecionar a Razor classe C# gerada para uma exibição](#inspect-the-razor-c-class-generated-for-a-view) explica como exibir essa classe gerada.

### `@attribute`

A diretiva `@attribute` adiciona o atributo fornecido à classe da página ou exibição gerada. O exemplo a seguir adiciona o atributo `[Authorize]`:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

O `@code` bloco permite que um [ Razor componente](xref:blazor/components/index) adicione Membros C# (campos, propriedades e métodos) a um componente:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Para Razor componentes `@code` do, é um alias de [`@functions`](#functions) e é recomendado `@functions` . Mais de um bloco de `@code` é permitido.

::: moniker-end

### `@functions`

A diretiva `@functions` permite adicionar membros (campos, propriedades e métodos) de C# à classe gerada:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

Em [ Razor componentes](xref:blazor/components/index), use `@code` `@functions` para adicionar Membros C#.

::: moniker-end

Por exemplo:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

O código gera a seguinte marcação HTML:

```html
<div>From method: Hello</div>
```

O código a seguir é a Razor classe C# gerada:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

Os métodos `@functions` servem como métodos de modelagem quando têm uma marcação:

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

O código renderiza o HTML a seguir:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

A diretiva `@implements` implementa uma interface para a classe gerada.

O exemplo a seguir implementa <xref:System.IDisposable?displayProperty=fullName> para que o método <xref:System.IDisposable.Dispose*> possa ser chamado:

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### `@inherits`

A diretiva `@inherits` fornece controle total da classe que a exibição herda:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

O código a seguir é um Razor tipo de página personalizada:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

O `CustomText` é exibido em uma exibição:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

O código renderiza o HTML a seguir:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` e `@inherits` podem ser usados na mesma exibição. `@inherits` pode estar em um arquivo *_ViewImports.cshtml* que a exibição importa:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

O código a seguir é um exemplo de exibição fortemente tipada:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Se "rick@contoso.com" for passado no modelo, a exibição gerará a seguinte marcação HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

A `@inject` diretiva permite que a Razor página Insira um serviço do [contêiner de serviço](xref:fundamentals/dependency-injection) em uma exibição. Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### `@layout`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

A `@layout` diretiva especifica um layout para um Razor componente. Os componentes de layout são usados para evitar casos de duplicação e inconsistência no código. Para obter mais informações, consulte <xref:blazor/layouts>.

::: moniker-end

### `@model`

*Este cenário só se aplica a exibições e Razor páginas do MVC (. cshtml).*

A diretiva `@model` especifica o tipo do modelo passado para uma exibição ou página:

```cshtml
@model TypeNameOfModel
```

Em um ASP.NET Core aplicativo MVC ou Razor páginas criado com contas de usuário individuais, *views/Account/Login. cshtml* contém a seguinte declaração de modelo:

```cshtml
@model LoginViewModel
```

A classe gerada herda de `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor expõe uma `Model` propriedade para acessar o modelo passado para a exibição:

```cshtml
<div>The Login Email: @Model.Email</div>
```

A diretiva `@model` especifica o tipo da propriedade `Model`. A diretiva especifica o `T` em `RazorPage<T>` da classe gerada da qual a exibição deriva. Se a diretiva `@model` não for especificada, a propriedade `Model` será do tipo `dynamic`. Para obter mais informações, consulte [modelos fortemente tipados e a @model palavra-chave](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### `@namespace`

A diretiva `@namespace`:

* Define o namespace da classe da Razor página gerada, da exibição MVC ou do Razor componente.
* Define os namespaces raiz derivados de páginas, exibições ou classes de componentes do arquivo de importações mais próximo na árvore de diretórios, *_ViewImports. cshtml* (exibições ou páginas) ou *_Imports. Razor* ( Razor componentes).

```cshtml
@namespace Your.Namespace.Here
```

Para o Razor exemplo de páginas mostrado na tabela a seguir:

* Cada página importa *Pages/_ViewImports.cshtml*.
* *Pages/_ViewImports.cshtml* contém `@namespace Hello.World`.
* Cada página tem `Hello.World` como a raiz do namespace.

| Página                                        | Namespace                             |
| ------------------------------------------- | ------------------------------------- |
| *Páginas/index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

As relações anteriores se aplicam a arquivos de importação usados com exibições e componentes do MVC Razor .

Quando vários arquivos de importação têm uma diretiva `@namespace`, o arquivo mais próximo da página, exibição ou componente na árvore de diretórios é usado para definir o namespace raiz.

Se a pasta *EvenMorePages* no exemplo anterior tiver um arquivo de importações com `@namespace Another.Planet` (ou se o arquivo *Pages/MorePages/EvenMorePages/Page.cshtml* contiver `@namespace Another.Planet`), o resultado será mostrado na tabela a seguir.

| Página                                        | Namespace               |
| ------------------------------------------- | ----------------------- |
| *Páginas/index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

A diretiva `@page` tem efeitos diferentes dependendo do tipo do arquivo em que aparece. A diretiva:

* Em um arquivo *. cshtml* indica que o arquivo é uma Razor página. Para obter mais informações, consulte [rotas personalizadas](xref:razor-pages/index#custom-routes) e <xref:razor-pages/index> .
* Especifica que um Razor componente deve tratar as solicitações diretamente. Para obter mais informações, consulte <xref:blazor/fundamentals/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

A `@page` diretiva na primeira linha de um arquivo *. cshtml* indica que o arquivo é uma Razor página. Para obter mais informações, consulte <xref:razor-pages/index>.

::: moniker-end

### `@section`

*Este cenário só se aplica a exibições e Razor páginas do MVC (. cshtml).*

A `@section` diretiva é usada em conjunto com o [MVC e Razor layouts de páginas](xref:mvc/views/layout) para permitir exibições ou páginas para renderizar conteúdo em diferentes partes da página HTML. Para obter mais informações, consulte <xref:mvc/views/layout>.

### `@using`

A diretiva `@using` adiciona a diretiva `using` de C# à exibição gerada:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

Em [ Razor componentes](xref:blazor/components/index), `@using` o também controla quais componentes estão no escopo.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Atributos de diretiva

Razor os atributos de diretiva são representados por expressões implícitas com palavras-chave reservadas após o `@` símbolo. Um atributo de diretiva normalmente altera a maneira como um elemento é analisado ou habilita uma funcionalidade diferente.

### `@attributes`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

`@attributes` permite que um componente renderize atributos não declarados. Para obter mais informações, consulte <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

### `@bind`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

A vinculação de dados nos componentes é realizada com o atributo `@bind`. Para obter mais informações, consulte <xref:blazor/components/data-binding>.

### `@on{EVENT}`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

Razor fornece recursos de manipulação de eventos para componentes do. Para obter mais informações, consulte <xref:blazor/components/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

Impede a ação padrão para o evento.

### `@on{EVENT}:stopPropagation`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

Interrompe a propagação do evento.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

O atributo da diretiva `@key` faz com que os componentes comparem o algoritmo para garantir a preservação de elementos ou componentes com base no valor da chave. Para obter mais informações, consulte <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.

### `@ref`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

Referências de componente (`@ref`) proporcionam uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância. Para obter mais informações, consulte <xref:blazor/components/index#capture-references-to-components>.

### `@typeparam`

*Esse cenário se aplica somente a Razor componentes (. Razor).*

A `@typeparam` diretiva declara um parâmetro de tipo genérico para a classe de componente gerada. Para obter mais informações, consulte <xref:blazor/components/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a>Delegados de modelo Razor

Razor os modelos permitem que você defina um trecho de código de interface do usuário com o seguinte formato:

```cshtml
@<tag>...</tag>
```

O exemplo a seguir ilustra como especificar um delegado de modelo Razor como um <xref:System.Func%602> . O [tipo dinâmico](/dotnet/csharp/programming-guide/types/using-type-dynamic) é especificado para o parâmetro do método encapsulado pelo delegado. Um [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) é especificado como o valor retornado do delegado. O modelo é usado com uma <xref:System.Collections.Generic.List%601> de `Pet` que tem uma propriedade `Name`.

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

O modelo é renderizado com `pets` fornecido por uma instrução `foreach`:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Saída renderizada:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Você também pode fornecer um modelo embutido Razor como um argumento para um método. No exemplo a seguir, o `Repeat` método recebe um Razor modelo. O método usa o modelo para produzir o conteúdo HTML com repetições de itens fornecidos em uma lista:

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

Usando a lista de animais de estimação do exemplo anterior, o método `Repeat` é chamado com:

* <xref:System.Collections.Generic.List%601> de `Pet`.
* Número de vezes que deve ser repetido cada animal de estimação.
* Modelo embutido a ser usado para os itens da lista de uma lista não ordenada.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Saída renderizada:

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>Auxiliares de Marca

*Este cenário só se aplica a exibições e Razor páginas do MVC (. cshtml).*

Há três diretivas que relacionadas aos [Auxiliares de marca](xref:mvc/views/tag-helpers/intro).

| Diretiva | Função |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Disponibiliza os Auxiliares de marca para uma exibição. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Remove os Auxiliares de marca adicionados anteriormente de uma exibição. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Especifica um prefixo de marca para habilitar o suporte do Auxiliar de marca e tornar explícito o uso do Auxiliar de marca. |

## <a name="no-locrazor-reserved-keywords"></a>Razor Palavras-chave reservadas

### <a name="no-locrazor-keywords"></a>Razor Palavras-chave

* `page` (Requer o ASP.NET Core 2,1 ou posterior)
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* `helper` (Não tem suporte no momento por ASP.NET Core)

Razor as palavras-chave têm escape `@(Razor Keyword)` (por exemplo, `@(functions)` ).

### <a name="c-no-locrazor-keywords"></a>RazorPalavras-chave do C#

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

As Razor palavras-chave do C# devem ter um escape duplo com `@(@C# Razor Keyword)` (por exemplo, `@(@case)` ). O primeiro `@` escapa o Razor analisador. O segundo `@` faz o escape do analisador C#.

### <a name="reserved-keywords-not-used-by-no-locrazor"></a>Palavras-chave reservadas não usadas por Razor

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a>Inspecionar a Razor classe C# gerada para uma exibição

::: moniker range=">= aspnetcore-2.1"

Com o SDK do .NET Core 2,1 ou posterior, o [ Razor SDK](xref:razor-pages/sdk) manipula a compilação de Razor arquivos. Ao criar um projeto, o Razor SDK gera um *obj/<build_configuration>/<target_framework_moniker>/ Razor * Directory na raiz do projeto. A estrutura de diretório no *Razor* diretório espelha a estrutura de diretório do projeto.

Considere a seguinte estrutura de diretório em um projeto de páginas ASP.NET Core 2,1 Razor direcionado ao .NET Core 2,1:

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

A criação do projeto na configuração de *Depuração* produz o seguinte diretório *obj*:

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

Para exibir a classe gerada para *pages/index. cshtml*, abra *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Adicione a seguinte classe ao projeto do ASP.NET Core MVC:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

Em `Startup.ConfigureServices`, substitua o `RazorTemplateEngine` adicionado pelo MVC pela classe `CustomTemplateEngine`:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Defina o ponto de interrupção `CustomTemplateEngine` na instrução `return csharpDocument;`. Quando a execução do programa for interrompida no ponto de interrupção, veja o valor de `generatedCode`.

![Exibição do Visualizador de Texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Pesquisas de exibição e diferenciação de maiúsculas e minúsculas

O Razor mecanismo de exibição executa pesquisas que diferenciam maiúsculas de minúsculas para exibições. No entanto, a pesquisa real é determinada pelo sistema de arquivos subjacente:

* Origem baseada em arquivo:
  * Em sistemas operacionais com sistemas de arquivos que não diferenciam maiúsculas e minúsculas (por exemplo, Windows), pesquisas no provedor de arquivos físico não diferenciam maiúsculas de minúsculas. Por exemplo, `return View("Test")` resulta em correspondências para */Views/Home/Test.cshtml*, */Views/home/test.cshtml* e qualquer outra variação de maiúsculas e minúsculas.
  * Em sistemas de arquivos que diferenciam maiúsculas de minúsculas (por exemplo, Linux, OSX e com `EmbeddedFileProvider`), as pesquisas diferenciam maiúsculas de minúsculas. Por exemplo, `return View("Test")` corresponde especificamente a */Views/Home/Test.cshtml*.
* Exibições pré-compiladas: com o ASP.NET Core 2.0 e posteriores, pesquisar em exibições pré-compiladas não diferencia maiúsculas de minúsculas em nenhum sistema operacional. O comportamento é idêntico ao comportamento do provedor de arquivos físico no Windows. Se duas exibições pré-compiladas diferirem apenas quanto ao padrão de maiúsculas e minúsculas, o resultado da pesquisa não será determinístico.

Os desenvolvedores são incentivados a fazer a correspondência entre as maiúsculas e minúsculas dos nomes dos arquivos e de diretórios com o uso de maiúsculas e minúsculas em:

* Nomes de área, controlador e ação.
* Razor Pages.

Fazer essa correspondência garante que as implantações encontrem suas exibições, independentemente do sistema de arquivos subjacente.

## <a name="additional-resources"></a>Recursos adicionais

[Introdução à programação da Web do ASP.NET usando o Razor A sintaxe](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) fornece muitos exemplos de programação com Razor sintaxe.
