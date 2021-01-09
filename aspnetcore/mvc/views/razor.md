---
title: referência de sintaxe do Razor para ASP.NET Core
author: rick-anderson
description: Saiba mais sobre Razor a sintaxe de marcação para inserir código baseado em servidor em páginas da Web.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: cb9ffab19062bf726dd519c782d502f76e372073
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058279"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="52e3f-103">Razor referência de sintaxe para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52e3f-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="52e3f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)e [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="52e3f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="52e3f-105">Razor é uma sintaxe de marcação para inserir código baseado em servidor em páginas da Web.</span><span class="sxs-lookup"><span data-stu-id="52e3f-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="52e3f-106">A Razor sintaxe consiste em Razor marcação, C# e HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="52e3f-107">Os arquivos que contêm Razor geralmente têm uma extensão de arquivo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="52e3f-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="52e3f-108">Razortambém é encontrado em arquivos de [ Razor componentes](xref:blazor/components/index) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="52e3f-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="52e3f-109">Renderização de HTML</span><span class="sxs-lookup"><span data-stu-id="52e3f-109">Rendering HTML</span></span>

<span data-ttu-id="52e3f-110">O Razor idioma padrão é HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-110">The default Razor language is HTML.</span></span> <span data-ttu-id="52e3f-111">O processamento de HTML da Razor marcação não é diferente do processamento de HTML de um arquivo HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="52e3f-112">A marcação HTML em arquivos *. cshtml* Razor é renderizada pelo servidor inalterado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="52e3f-113">Sintaxe de Razor</span><span class="sxs-lookup"><span data-stu-id="52e3f-113">Razor syntax</span></span>

<span data-ttu-id="52e3f-114">Razor dá suporte a C# e usa o `@` símbolo para fazer a transição de HTML para C#.</span><span class="sxs-lookup"><span data-stu-id="52e3f-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="52e3f-115">Razor avalia as expressões C# e as renderiza na saída HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="52e3f-116">Quando um `@` símbolo é seguido por uma [ Razor palavra-chave reservada](#razor-reserved-keywords), ele faz a transição para uma Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="52e3f-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="52e3f-117">Caso contrário, ele faz a transição para C# simples.</span><span class="sxs-lookup"><span data-stu-id="52e3f-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="52e3f-118">Para escapar de um `@` símbolo na Razor marcação, use um segundo `@` símbolo:</span><span class="sxs-lookup"><span data-stu-id="52e3f-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="52e3f-119">O código é renderizado em HTML com um único símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="52e3f-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="52e3f-120">Conteúdo e atributos HTML que contêm endereços de email não tratam o símbolo `@` como um caractere de transição.</span><span class="sxs-lookup"><span data-stu-id="52e3f-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="52e3f-121">Os endereços de email no exemplo a seguir são inalterados pela Razor análise:</span><span class="sxs-lookup"><span data-stu-id="52e3f-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="52e3f-122">Expressões implícitas Razor</span><span class="sxs-lookup"><span data-stu-id="52e3f-122">Implicit Razor expressions</span></span>

<span data-ttu-id="52e3f-123">As Razor expressões implícitas começam com `@` seguido pelo código C#:</span><span class="sxs-lookup"><span data-stu-id="52e3f-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="52e3f-124">Com exceção da palavra-chave C# `await`, expressões implícitas não devem conter espaços.</span><span class="sxs-lookup"><span data-stu-id="52e3f-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="52e3f-125">Se a instrução C# tiver uma terminação clara, espaços podem ser misturados:</span><span class="sxs-lookup"><span data-stu-id="52e3f-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="52e3f-126">Expressões implícitas **não podem** conter elementos genéricos de C#, pois caracteres dentro de colchetes (`<>`) são interpretados como uma marca HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="52e3f-127">O código a seguir é **inválido**:</span><span class="sxs-lookup"><span data-stu-id="52e3f-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="52e3f-128">O código anterior gera um erro de compilador semelhante a um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="52e3f-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="52e3f-129">O elemento "int" não foi fechado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="52e3f-130">Todos os elementos devem ter fechamento automático ou ter uma marca de fim correspondente.</span><span class="sxs-lookup"><span data-stu-id="52e3f-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="52e3f-131">Não é possível converter o grupo de métodos "GenericMethod" em um "object" de tipo não delegado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="52e3f-132">Você pretendia invocar o método?</span><span class="sxs-lookup"><span data-stu-id="52e3f-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="52e3f-133">Chamadas de método genérico devem ser encapsuladas em uma [ Razor expressão explícita](#explicit-razor-expressions) ou em um [ Razor bloco de código](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="52e3f-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="52e3f-134">Expressões explícitas Razor</span><span class="sxs-lookup"><span data-stu-id="52e3f-134">Explicit Razor expressions</span></span>

<span data-ttu-id="52e3f-135">Expressões explícitas Razor consistem em um `@` símbolo com parênteses equilibrados.</span><span class="sxs-lookup"><span data-stu-id="52e3f-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="52e3f-136">Para renderizar a hora da semana passada, a seguinte Razor marcação é usada:</span><span class="sxs-lookup"><span data-stu-id="52e3f-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="52e3f-137">Qualquer conteúdo dentro dos parênteses `@()` é avaliado e renderizado para a saída.</span><span class="sxs-lookup"><span data-stu-id="52e3f-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="52e3f-138">Expressões implícitas, descritas na seção anterior, geralmente não podem conter espaços.</span><span class="sxs-lookup"><span data-stu-id="52e3f-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="52e3f-139">No código a seguir, uma semana não é subtraída da hora atual:</span><span class="sxs-lookup"><span data-stu-id="52e3f-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="52e3f-140">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="52e3f-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="52e3f-141">Expressões explícitas podem ser usadas para concatenar texto com um resultado de expressão:</span><span class="sxs-lookup"><span data-stu-id="52e3f-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="52e3f-142">Sem a expressão explícita, `<p>Age@joe.Age</p>` é tratado como um endereço de email e `<p>Age@joe.Age</p>` é renderizado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="52e3f-143">Quando escrito como uma expressão explícita, `<p>Age33</p>` é renderizado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="52e3f-144">Expressões explícitas podem ser usadas para renderizar a saída de métodos genéricos em arquivos *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="52e3f-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="52e3f-145">A marcação a seguir mostra como corrigir o erro mostrado anteriormente causado pelos colchetes de um C# genérico.</span><span class="sxs-lookup"><span data-stu-id="52e3f-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="52e3f-146">O código é escrito como uma expressão explícita:</span><span class="sxs-lookup"><span data-stu-id="52e3f-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="52e3f-147">Codificação de expressão</span><span class="sxs-lookup"><span data-stu-id="52e3f-147">Expression encoding</span></span>

<span data-ttu-id="52e3f-148">Expressões em C# que são avaliadas como uma cadeia de caracteres estão codificadas em HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="52e3f-149">Expressões em C# que são avaliadas como `IHtmlContent` são renderizadas diretamente por meio `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="52e3f-150">Expressões em C# que não são avaliadas como `IHtmlContent` são convertidas em uma cadeia de caracteres por `ToString` e codificadas antes que sejam renderizadas.</span><span class="sxs-lookup"><span data-stu-id="52e3f-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="52e3f-151">O código anterior processa o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="52e3f-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="52e3f-152">O HTML é mostrado no navegador como texto sem formatação:</span><span class="sxs-lookup"><span data-stu-id="52e3f-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="52e3f-153">&lt;&gt;Olá, mundo &lt; /span de span&gt;</span><span class="sxs-lookup"><span data-stu-id="52e3f-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="52e3f-154">A saída `HtmlHelper.Raw` não é codificada, mas renderizada como marcação HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="52e3f-155">Usar `HtmlHelper.Raw` em uma entrada do usuário que não está limpa é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="52e3f-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="52e3f-156">A entrada do usuário pode conter JavaScript mal-intencionado ou outras formas de exploração.</span><span class="sxs-lookup"><span data-stu-id="52e3f-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="52e3f-157">Limpar a entrada do usuário é difícil.</span><span class="sxs-lookup"><span data-stu-id="52e3f-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="52e3f-158">Evite usar `HtmlHelper.Raw` com a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="52e3f-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="52e3f-159">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="52e3f-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="52e3f-160">Razor blocos de código</span><span class="sxs-lookup"><span data-stu-id="52e3f-160">Razor code blocks</span></span>

<span data-ttu-id="52e3f-161">Razor os blocos de código começam com `@` e são incluídos por `{}` .</span><span class="sxs-lookup"><span data-stu-id="52e3f-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="52e3f-162">Diferente das expressões, o código C# dentro de blocos de código não é renderizado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="52e3f-163">Blocos de código e expressões em uma exibição compartilham o mesmo escopo e são definidos em ordem:</span><span class="sxs-lookup"><span data-stu-id="52e3f-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="52e3f-164">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="52e3f-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52e3f-165">Em blocos de código, declare [funções locais](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) com uma marcação para servir como métodos de modelagem:</span><span class="sxs-lookup"><span data-stu-id="52e3f-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="52e3f-166">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="52e3f-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="52e3f-167">Transições implícitas</span><span class="sxs-lookup"><span data-stu-id="52e3f-167">Implicit transitions</span></span>

<span data-ttu-id="52e3f-168">O idioma padrão em um bloco de código é C#, mas a Razor página pode fazer a transição de volta para o HTML:</span><span class="sxs-lookup"><span data-stu-id="52e3f-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="52e3f-169">Transição delimitada explícita</span><span class="sxs-lookup"><span data-stu-id="52e3f-169">Explicit delimited transition</span></span>

<span data-ttu-id="52e3f-170">Para definir uma subseção de um bloco de código que deve renderizar HTML, coloque os caracteres para renderização com a Razor `<text>` marca:</span><span class="sxs-lookup"><span data-stu-id="52e3f-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="52e3f-171">Use essa abordagem para renderizar HTML que não está circundado por uma marca HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="52e3f-172">Sem um HTML ou Razor marca, Razor ocorre um erro de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="52e3f-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="52e3f-173">A marca `<text>` é útil para controlar o espaço em branco ao renderizar conteúdo:</span><span class="sxs-lookup"><span data-stu-id="52e3f-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="52e3f-174">Somente o conteúdo entre a marca `<text>` é renderizado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="52e3f-175">Não aparece nenhum espaço em branco antes ou depois da marca `<text>` na saída HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="52e3f-176">Transição de linha explícita</span><span class="sxs-lookup"><span data-stu-id="52e3f-176">Explicit line transition</span></span>

<span data-ttu-id="52e3f-177">Para renderizar o restante de uma linha inteira como HTML dentro de um bloco de código, use a `@:` sintaxe:</span><span class="sxs-lookup"><span data-stu-id="52e3f-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="52e3f-178">Sem o `@:` no código, um Razor erro de tempo de execução é gerado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="52e3f-179">`@`Caracteres extras em um Razor arquivo podem causar erros de compilador em instruções posteriores no bloco.</span><span class="sxs-lookup"><span data-stu-id="52e3f-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="52e3f-180">Esses erros do compilador podem ser difíceis de entender porque o erro real ocorre antes do erro relatado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="52e3f-181">Esse erro é comum após combinar várias expressões implícitas/explícitas em um bloco de código único.</span><span class="sxs-lookup"><span data-stu-id="52e3f-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="52e3f-182">Estruturas de controle</span><span class="sxs-lookup"><span data-stu-id="52e3f-182">Control structures</span></span>

<span data-ttu-id="52e3f-183">Estruturas de controle são uma extensão dos blocos de código.</span><span class="sxs-lookup"><span data-stu-id="52e3f-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="52e3f-184">Todos os aspectos dos blocos de código (transição para marcação, C# embutido) também se aplicam às seguintes estruturas:</span><span class="sxs-lookup"><span data-stu-id="52e3f-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="52e3f-185">Condicionais `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="52e3f-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="52e3f-186">`@if` controla quando o código é executado:</span><span class="sxs-lookup"><span data-stu-id="52e3f-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="52e3f-187">`else` e `else if` não exigem o símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="52e3f-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="52e3f-188">A marcação a seguir mostra como usar uma instrução switch:</span><span class="sxs-lookup"><span data-stu-id="52e3f-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="52e3f-189">Loop `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="52e3f-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="52e3f-190">O HTML no modelo pode ser renderizado com instruções de controle em loop.</span><span class="sxs-lookup"><span data-stu-id="52e3f-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="52e3f-191">Para renderizar uma lista de pessoas:</span><span class="sxs-lookup"><span data-stu-id="52e3f-191">To render a list of people:</span></span>

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

<span data-ttu-id="52e3f-192">Há suporte para as seguintes instruções em loop:</span><span class="sxs-lookup"><span data-stu-id="52e3f-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="52e3f-193">`@using` composto</span><span class="sxs-lookup"><span data-stu-id="52e3f-193">Compound `@using`</span></span>

<span data-ttu-id="52e3f-194">Em C#, uma instrução `using` é usada para garantir que um objeto seja descartado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="52e3f-195">No Razor , o mesmo mecanismo é usado para criar auxiliares HTML que contêm conteúdo adicional.</span><span class="sxs-lookup"><span data-stu-id="52e3f-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="52e3f-196">No código a seguir, os Auxiliares HTML renderizam uma marca `<form>` com a instrução `@using`:</span><span class="sxs-lookup"><span data-stu-id="52e3f-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="52e3f-197">O tratamento de exceções é semelhante ao de C#:</span><span class="sxs-lookup"><span data-stu-id="52e3f-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="52e3f-198">Razor o tem a capacidade de proteger seções críticas com instruções Lock:</span><span class="sxs-lookup"><span data-stu-id="52e3f-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="52e3f-199">Comentários</span><span class="sxs-lookup"><span data-stu-id="52e3f-199">Comments</span></span>

<span data-ttu-id="52e3f-200">Razor dá suporte a comentários em C# e HTML:</span><span class="sxs-lookup"><span data-stu-id="52e3f-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="52e3f-201">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="52e3f-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="52e3f-202">Razor Os comentários são removidos pelo servidor antes que a página da Web seja renderizada.</span><span class="sxs-lookup"><span data-stu-id="52e3f-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="52e3f-203">Razor usa `@*  *@` para delimitar comentários.</span><span class="sxs-lookup"><span data-stu-id="52e3f-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="52e3f-204">O código a seguir é comentado, de modo que o servidor não renderiza nenhuma marcação:</span><span class="sxs-lookup"><span data-stu-id="52e3f-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="52e3f-205">Diretivas</span><span class="sxs-lookup"><span data-stu-id="52e3f-205">Directives</span></span>

<span data-ttu-id="52e3f-206">Razor as diretivas são representadas por expressões implícitas com palavras-chave reservadas após o `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="52e3f-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="52e3f-207">Uma diretiva geralmente altera o modo como uma exibição é analisada ou habilita uma funcionalidade diferente.</span><span class="sxs-lookup"><span data-stu-id="52e3f-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="52e3f-208">Entender como o Razor gera código para uma exibição torna mais fácil entender como as diretivas funcionam.</span><span class="sxs-lookup"><span data-stu-id="52e3f-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="52e3f-209">O código gera uma classe semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="52e3f-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="52e3f-210">Posteriormente neste artigo, a seção [inspecionar a Razor classe C# gerada para uma exibição](#inspect-the-razor-c-class-generated-for-a-view) explica como exibir essa classe gerada.</span><span class="sxs-lookup"><span data-stu-id="52e3f-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="52e3f-211">A diretiva `@attribute` adiciona o atributo fornecido à classe da página ou exibição gerada.</span><span class="sxs-lookup"><span data-stu-id="52e3f-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="52e3f-212">O exemplo a seguir adiciona o atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="52e3f-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="52e3f-213">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-214">O `@code` bloco permite que um [ Razor componente](xref:blazor/components/index) adicione Membros C# (campos, propriedades e métodos) a um componente:</span><span class="sxs-lookup"><span data-stu-id="52e3f-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="52e3f-215">Para Razor componentes `@code` do, é um alias de [`@functions`](#functions) e é recomendado `@functions` .</span><span class="sxs-lookup"><span data-stu-id="52e3f-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="52e3f-216">Mais de um bloco de `@code` é permitido.</span><span class="sxs-lookup"><span data-stu-id="52e3f-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="52e3f-217">A diretiva `@functions` permite adicionar membros (campos, propriedades e métodos) de C# à classe gerada:</span><span class="sxs-lookup"><span data-stu-id="52e3f-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52e3f-218">Em [ Razor componentes](xref:blazor/components/index), use `@code` `@functions` para adicionar Membros C#.</span><span class="sxs-lookup"><span data-stu-id="52e3f-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="52e3f-219">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="52e3f-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="52e3f-220">O código gera a seguinte marcação HTML:</span><span class="sxs-lookup"><span data-stu-id="52e3f-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="52e3f-221">O código a seguir é a Razor classe C# gerada:</span><span class="sxs-lookup"><span data-stu-id="52e3f-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52e3f-222">Os métodos `@functions` servem como métodos de modelagem quando têm uma marcação:</span><span class="sxs-lookup"><span data-stu-id="52e3f-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="52e3f-223">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="52e3f-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="52e3f-224">A diretiva `@implements` implementa uma interface para a classe gerada.</span><span class="sxs-lookup"><span data-stu-id="52e3f-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="52e3f-225">O exemplo a seguir implementa <xref:System.IDisposable?displayProperty=fullName> para que o método <xref:System.IDisposable.Dispose*> possa ser chamado:</span><span class="sxs-lookup"><span data-stu-id="52e3f-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="52e3f-226">A diretiva `@inherits` fornece controle total da classe que a exibição herda:</span><span class="sxs-lookup"><span data-stu-id="52e3f-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="52e3f-227">O código a seguir é um Razor tipo de página personalizada:</span><span class="sxs-lookup"><span data-stu-id="52e3f-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="52e3f-228">O `CustomText` é exibido em uma exibição:</span><span class="sxs-lookup"><span data-stu-id="52e3f-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="52e3f-229">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="52e3f-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="52e3f-230">`@model` e `@inherits` podem ser usados na mesma exibição.</span><span class="sxs-lookup"><span data-stu-id="52e3f-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="52e3f-231">`@inherits` pode estar em um arquivo *_ViewImports.cshtml* que a exibição importa:</span><span class="sxs-lookup"><span data-stu-id="52e3f-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="52e3f-232">O código a seguir é um exemplo de exibição fortemente tipada:</span><span class="sxs-lookup"><span data-stu-id="52e3f-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="52e3f-233">Se "rick@contoso.com" for passado no modelo, a exibição gerará a seguinte marcação HTML:</span><span class="sxs-lookup"><span data-stu-id="52e3f-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="52e3f-234">A `@inject` diretiva permite que a Razor página Insira um serviço do [contêiner de serviço](xref:fundamentals/dependency-injection) em uma exibição.</span><span class="sxs-lookup"><span data-stu-id="52e3f-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="52e3f-235">Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="52e3f-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="52e3f-236">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-237">A `@layout` diretiva especifica um layout para componentes roteáveis Razor que têm uma [`@page`](#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="52e3f-237">The `@layout` directive specifies a layout for routable Razor components that have an [`@page`](#page) directive.</span></span> <span data-ttu-id="52e3f-238">Os componentes de layout são usados para evitar casos de duplicação e inconsistência no código.</span><span class="sxs-lookup"><span data-stu-id="52e3f-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="52e3f-239">Para obter mais informações, consulte <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="52e3f-240">*Este cenário só se aplica a exibições e Razor páginas do MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="52e3f-241">A diretiva `@model` especifica o tipo do modelo passado para uma exibição ou página:</span><span class="sxs-lookup"><span data-stu-id="52e3f-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="52e3f-242">Em um ASP.NET Core aplicativo MVC ou Razor páginas criado com contas de usuário individuais, *views/Account/Login. cshtml* contém a seguinte declaração de modelo:</span><span class="sxs-lookup"><span data-stu-id="52e3f-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="52e3f-243">A classe gerada herda de `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="52e3f-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="52e3f-244">Razor expõe uma `Model` propriedade para acessar o modelo passado para a exibição:</span><span class="sxs-lookup"><span data-stu-id="52e3f-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="52e3f-245">A diretiva `@model` especifica o tipo da propriedade `Model`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="52e3f-246">A diretiva especifica o `T` em `RazorPage<T>` da classe gerada da qual a exibição deriva.</span><span class="sxs-lookup"><span data-stu-id="52e3f-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="52e3f-247">Se a diretiva `@model` não for especificada, a propriedade `Model` será do tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="52e3f-248">Para obter mais informações, consulte [modelos fortemente tipados e a @model palavra-chave](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="52e3f-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="52e3f-249">A diretiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="52e3f-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="52e3f-250">Define o namespace da classe da Razor página gerada, da exibição MVC ou do Razor componente.</span><span class="sxs-lookup"><span data-stu-id="52e3f-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="52e3f-251">Define os namespaces raiz derivados de páginas, exibições ou classes de componentes do arquivo de importações mais próximo na árvore de diretórios, *_ViewImports. cshtml* (exibições ou páginas) ou *_Imports. Razor* ( Razor componentes).</span><span class="sxs-lookup"><span data-stu-id="52e3f-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="52e3f-252">Para o Razor exemplo de páginas mostrado na tabela a seguir:</span><span class="sxs-lookup"><span data-stu-id="52e3f-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="52e3f-253">Cada página importa *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="52e3f-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="52e3f-254">*Pages/_ViewImports.cshtml* contém `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="52e3f-255">Cada página tem `Hello.World` como a raiz do namespace.</span><span class="sxs-lookup"><span data-stu-id="52e3f-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="52e3f-256">Página</span><span class="sxs-lookup"><span data-stu-id="52e3f-256">Page</span></span>                                        | <span data-ttu-id="52e3f-257">Namespace</span><span class="sxs-lookup"><span data-stu-id="52e3f-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="52e3f-258">*Páginas/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="52e3f-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="52e3f-259">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52e3f-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="52e3f-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52e3f-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="52e3f-261">As relações anteriores se aplicam a arquivos de importação usados com exibições e componentes do MVC Razor .</span><span class="sxs-lookup"><span data-stu-id="52e3f-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="52e3f-262">Quando vários arquivos de importação têm uma diretiva `@namespace`, o arquivo mais próximo da página, exibição ou componente na árvore de diretórios é usado para definir o namespace raiz.</span><span class="sxs-lookup"><span data-stu-id="52e3f-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="52e3f-263">Se a pasta *EvenMorePages* no exemplo anterior tiver um arquivo de importações com `@namespace Another.Planet` (ou se o arquivo *Pages/MorePages/EvenMorePages/Page.cshtml* contiver `@namespace Another.Planet`), o resultado será mostrado na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="52e3f-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="52e3f-264">Página</span><span class="sxs-lookup"><span data-stu-id="52e3f-264">Page</span></span>                                        | <span data-ttu-id="52e3f-265">Namespace</span><span class="sxs-lookup"><span data-stu-id="52e3f-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="52e3f-266">*Páginas/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="52e3f-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="52e3f-267">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52e3f-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="52e3f-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52e3f-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52e3f-269">A diretiva `@page` tem efeitos diferentes dependendo do tipo do arquivo em que aparece.</span><span class="sxs-lookup"><span data-stu-id="52e3f-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="52e3f-270">A diretiva:</span><span class="sxs-lookup"><span data-stu-id="52e3f-270">The directive:</span></span>

* <span data-ttu-id="52e3f-271">Em um arquivo *. cshtml* indica que o arquivo é uma Razor página.</span><span class="sxs-lookup"><span data-stu-id="52e3f-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="52e3f-272">Para obter mais informações, consulte [rotas personalizadas](xref:razor-pages/index#custom-routes) e <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="52e3f-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="52e3f-273">Especifica que um Razor componente deve tratar as solicitações diretamente.</span><span class="sxs-lookup"><span data-stu-id="52e3f-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="52e3f-274">Para obter mais informações, consulte <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52e3f-275">A `@page` diretiva na primeira linha de um arquivo *. cshtml* indica que o arquivo é uma Razor página.</span><span class="sxs-lookup"><span data-stu-id="52e3f-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="52e3f-276">Para obter mais informações, consulte <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

<span data-ttu-id="52e3f-277">*Esse cenário se aplica somente aos Razor componentes ( `.razor` ).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-277">*This scenario only applies to Razor components (`.razor`).*</span></span>

<span data-ttu-id="52e3f-278">Quando definido como `false` (padrão), o espaço em branco na marcação renderizada de Razor Components ( `.razor` ) será removido se:</span><span class="sxs-lookup"><span data-stu-id="52e3f-278">When set to `false` (default), whitespace in the rendered markup from Razor components (`.razor`) is removed if:</span></span>

* <span data-ttu-id="52e3f-279">À esquerda ou à direita dentro de um elemento.</span><span class="sxs-lookup"><span data-stu-id="52e3f-279">Leading or trailing within an element.</span></span>
* <span data-ttu-id="52e3f-280">À esquerda ou à direita dentro de um `RenderFragment` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="52e3f-280">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="52e3f-281">Por exemplo, conteúdo filho passado para outro componente.</span><span class="sxs-lookup"><span data-stu-id="52e3f-281">For example, child content passed to another component.</span></span>
* <span data-ttu-id="52e3f-282">Ele precede ou segue um bloco de código C#, como `@if` ou `@foreach` .</span><span class="sxs-lookup"><span data-stu-id="52e3f-282">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="52e3f-283">*Este cenário só se aplica a exibições e Razor páginas do MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-283">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="52e3f-284">A `@section` diretiva é usada em conjunto com o [MVC e Razor layouts de páginas](xref:mvc/views/layout) para permitir exibições ou páginas para renderizar conteúdo em diferentes partes da página HTML.</span><span class="sxs-lookup"><span data-stu-id="52e3f-284">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="52e3f-285">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-285">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="52e3f-286">A diretiva `@using` adiciona a diretiva `using` de C# à exibição gerada:</span><span class="sxs-lookup"><span data-stu-id="52e3f-286">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52e3f-287">Em [ Razor componentes](xref:blazor/components/index), `@using` o também controla quais componentes estão no escopo.</span><span class="sxs-lookup"><span data-stu-id="52e3f-287">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="52e3f-288">Atributos de diretiva</span><span class="sxs-lookup"><span data-stu-id="52e3f-288">Directive attributes</span></span>

<span data-ttu-id="52e3f-289">Razor os atributos de diretiva são representados por expressões implícitas com palavras-chave reservadas após o `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="52e3f-289">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="52e3f-290">Um atributo de diretiva normalmente altera a maneira como um elemento é analisado ou habilita uma funcionalidade diferente.</span><span class="sxs-lookup"><span data-stu-id="52e3f-290">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="52e3f-291">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-292">`@attributes` permite que um componente renderize atributos não declarados.</span><span class="sxs-lookup"><span data-stu-id="52e3f-292">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="52e3f-293">Para obter mais informações, consulte <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-293">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="52e3f-294">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-295">A vinculação de dados nos componentes é realizada com o atributo `@bind`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-295">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="52e3f-296">Para obter mais informações, consulte <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-296">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="52e3f-297">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-298">Razor fornece recursos de manipulação de eventos para componentes do.</span><span class="sxs-lookup"><span data-stu-id="52e3f-298">Razor provides event handling features for components.</span></span> <span data-ttu-id="52e3f-299">Para obter mais informações, consulte <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-299">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="52e3f-300">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-301">Impede a ação padrão para o evento.</span><span class="sxs-lookup"><span data-stu-id="52e3f-301">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="52e3f-302">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-302">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-303">Interrompe a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="52e3f-303">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="52e3f-304">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-305">O atributo da diretiva `@key` faz com que os componentes comparem o algoritmo para garantir a preservação de elementos ou componentes com base no valor da chave.</span><span class="sxs-lookup"><span data-stu-id="52e3f-305">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="52e3f-306">Para obter mais informações, consulte <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-306">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="52e3f-307">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-308">Referências de componente (`@ref`) proporcionam uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância.</span><span class="sxs-lookup"><span data-stu-id="52e3f-308">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="52e3f-309">Para obter mais informações, consulte <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-309">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="52e3f-310">*Esse cenário se aplica somente a Razor componentes (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-310">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52e3f-311">A `@typeparam` diretiva declara um parâmetro de tipo genérico para a classe de componente gerada.</span><span class="sxs-lookup"><span data-stu-id="52e3f-311">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="52e3f-312">Para obter mais informações, consulte <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="52e3f-312">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="52e3f-313">Delegados de modelo Razor</span><span class="sxs-lookup"><span data-stu-id="52e3f-313">Templated Razor delegates</span></span>

<span data-ttu-id="52e3f-314">Razor os modelos permitem que você defina um trecho de código de interface do usuário com o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="52e3f-314">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="52e3f-315">O exemplo a seguir ilustra como especificar um delegado de modelo Razor como um <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="52e3f-315">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="52e3f-316">O [tipo dinâmico](/dotnet/csharp/programming-guide/types/using-type-dynamic) é especificado para o parâmetro do método encapsulado pelo delegado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-316">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="52e3f-317">Um [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) é especificado como o valor retornado do delegado.</span><span class="sxs-lookup"><span data-stu-id="52e3f-317">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="52e3f-318">O modelo é usado com uma <xref:System.Collections.Generic.List%601> de `Pet` que tem uma propriedade `Name`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-318">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="52e3f-319">O modelo é renderizado com `pets` fornecido por uma instrução `foreach`:</span><span class="sxs-lookup"><span data-stu-id="52e3f-319">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="52e3f-320">Saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="52e3f-320">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="52e3f-321">Você também pode fornecer um modelo embutido Razor como um argumento para um método.</span><span class="sxs-lookup"><span data-stu-id="52e3f-321">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="52e3f-322">No exemplo a seguir, o `Repeat` método recebe um Razor modelo.</span><span class="sxs-lookup"><span data-stu-id="52e3f-322">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="52e3f-323">O método usa o modelo para produzir o conteúdo HTML com repetições de itens fornecidos em uma lista:</span><span class="sxs-lookup"><span data-stu-id="52e3f-323">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="52e3f-324">Usando a lista de animais de estimação do exemplo anterior, o método `Repeat` é chamado com:</span><span class="sxs-lookup"><span data-stu-id="52e3f-324">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="52e3f-325"><xref:System.Collections.Generic.List%601> de `Pet`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-325"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="52e3f-326">Número de vezes que deve ser repetido cada animal de estimação.</span><span class="sxs-lookup"><span data-stu-id="52e3f-326">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="52e3f-327">Modelo embutido a ser usado para os itens da lista de uma lista não ordenada.</span><span class="sxs-lookup"><span data-stu-id="52e3f-327">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="52e3f-328">Saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="52e3f-328">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="52e3f-329">Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="52e3f-329">Tag Helpers</span></span>

<span data-ttu-id="52e3f-330">*Este cenário só se aplica a exibições e Razor páginas do MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="52e3f-330">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="52e3f-331">Há três diretivas que relacionadas aos [Auxiliares de marca](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="52e3f-331">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="52e3f-332">Diretiva</span><span class="sxs-lookup"><span data-stu-id="52e3f-332">Directive</span></span> | <span data-ttu-id="52e3f-333">Função</span><span class="sxs-lookup"><span data-stu-id="52e3f-333">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="52e3f-334">Disponibiliza os Auxiliares de marca para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="52e3f-334">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="52e3f-335">Remove os Auxiliares de marca adicionados anteriormente de uma exibição.</span><span class="sxs-lookup"><span data-stu-id="52e3f-335">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="52e3f-336">Especifica um prefixo de marca para habilitar o suporte do Auxiliar de marca e tornar explícito o uso do Auxiliar de marca.</span><span class="sxs-lookup"><span data-stu-id="52e3f-336">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="52e3f-337">Razor Palavras-chave reservadas</span><span class="sxs-lookup"><span data-stu-id="52e3f-337">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="52e3f-338">Razor Palavras-chave</span><span class="sxs-lookup"><span data-stu-id="52e3f-338">Razor keywords</span></span>

* <span data-ttu-id="52e3f-339">`page` (Requer o ASP.NET Core 2,1 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="52e3f-339">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="52e3f-340">`helper` (Não tem suporte no momento por ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="52e3f-340">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="52e3f-341">Razor as palavras-chave têm escape `@(Razor Keyword)` (por exemplo, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="52e3f-341">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="52e3f-342">RazorPalavras-chave do C#</span><span class="sxs-lookup"><span data-stu-id="52e3f-342">C# Razor keywords</span></span>

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

<span data-ttu-id="52e3f-343">As Razor palavras-chave do C# devem ter um escape duplo com `@(@C# Razor Keyword)` (por exemplo, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="52e3f-343">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="52e3f-344">O primeiro `@` escapa o Razor analisador.</span><span class="sxs-lookup"><span data-stu-id="52e3f-344">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="52e3f-345">O segundo `@` faz o escape do analisador C#.</span><span class="sxs-lookup"><span data-stu-id="52e3f-345">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="52e3f-346">Palavras-chave reservadas não usadas por Razor</span><span class="sxs-lookup"><span data-stu-id="52e3f-346">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="52e3f-347">Inspecionar a Razor classe C# gerada para uma exibição</span><span class="sxs-lookup"><span data-stu-id="52e3f-347">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="52e3f-348">Com o SDK do .NET Core 2,1 ou posterior, o [ Razor SDK](xref:razor-pages/sdk) manipula a compilação de Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="52e3f-348">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="52e3f-349">Ao criar um projeto, o Razor SDK gera um *obj/<build_configuration>/<target_framework_moniker>/ Razor* Directory na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="52e3f-349">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="52e3f-350">A estrutura de diretório no *Razor* diretório espelha a estrutura de diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="52e3f-350">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="52e3f-351">Considere a seguinte estrutura de diretório em um projeto de páginas ASP.NET Core 2,1 Razor direcionado ao .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="52e3f-351">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="52e3f-352">A criação do projeto na configuração de *Depuração* produz o seguinte diretório *obj*:</span><span class="sxs-lookup"><span data-stu-id="52e3f-352">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="52e3f-353">Para exibir a classe gerada para *pages/index. cshtml*, abra *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="52e3f-353">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="52e3f-354">Adicione a seguinte classe ao projeto do ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="52e3f-354">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="52e3f-355">Em `Startup.ConfigureServices`, substitua o `RazorTemplateEngine` adicionado pelo MVC pela classe `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="52e3f-355">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="52e3f-356">Defina o ponto de interrupção `CustomTemplateEngine` na instrução `return csharpDocument;`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-356">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="52e3f-357">Quando a execução do programa for interrompida no ponto de interrupção, veja o valor de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="52e3f-357">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Exibição do Visualizador de Texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="52e3f-359">Pesquisas de exibição e diferenciação de maiúsculas e minúsculas</span><span class="sxs-lookup"><span data-stu-id="52e3f-359">View lookups and case sensitivity</span></span>

<span data-ttu-id="52e3f-360">O Razor mecanismo de exibição executa pesquisas que diferenciam maiúsculas de minúsculas para exibições.</span><span class="sxs-lookup"><span data-stu-id="52e3f-360">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="52e3f-361">No entanto, a pesquisa real é determinada pelo sistema de arquivos subjacente:</span><span class="sxs-lookup"><span data-stu-id="52e3f-361">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="52e3f-362">Origem baseada em arquivo:</span><span class="sxs-lookup"><span data-stu-id="52e3f-362">File based source:</span></span>
  * <span data-ttu-id="52e3f-363">Em sistemas operacionais com sistemas de arquivos que não diferenciam maiúsculas e minúsculas (por exemplo, Windows), pesquisas no provedor de arquivos físico não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="52e3f-363">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="52e3f-364">Por exemplo, `return View("Test")` resulta em correspondências para */Views/Home/Test.cshtml*, */Views/home/test.cshtml* e qualquer outra variação de maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="52e3f-364">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="52e3f-365">Em sistemas de arquivos que diferenciam maiúsculas de minúsculas (por exemplo, Linux, OSX e com `EmbeddedFileProvider`), as pesquisas diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="52e3f-365">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="52e3f-366">Por exemplo, `return View("Test")` corresponde especificamente a */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="52e3f-366">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="52e3f-367">Exibições pré-compiladas: com o ASP.NET Core 2.0 e posteriores, pesquisar em exibições pré-compiladas não diferencia maiúsculas de minúsculas em nenhum sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="52e3f-367">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="52e3f-368">O comportamento é idêntico ao comportamento do provedor de arquivos físico no Windows.</span><span class="sxs-lookup"><span data-stu-id="52e3f-368">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="52e3f-369">Se duas exibições pré-compiladas diferirem apenas quanto ao padrão de maiúsculas e minúsculas, o resultado da pesquisa não será determinístico.</span><span class="sxs-lookup"><span data-stu-id="52e3f-369">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="52e3f-370">Os desenvolvedores são incentivados a fazer a correspondência entre as maiúsculas e minúsculas dos nomes dos arquivos e de diretórios com o uso de maiúsculas e minúsculas em:</span><span class="sxs-lookup"><span data-stu-id="52e3f-370">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="52e3f-371">Nomes de área, controlador e ação.</span><span class="sxs-lookup"><span data-stu-id="52e3f-371">Area, controller, and action names.</span></span>
* <span data-ttu-id="52e3f-372">Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="52e3f-372">Razor Pages.</span></span>

<span data-ttu-id="52e3f-373">Fazer essa correspondência garante que as implantações encontrem suas exibições, independentemente do sistema de arquivos subjacente.</span><span class="sxs-lookup"><span data-stu-id="52e3f-373">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52e3f-374">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="52e3f-374">Additional resources</span></span>

<span data-ttu-id="52e3f-375">[Introdução à programação da Web do ASP.NET usando o Razor A sintaxe](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) fornece muitos exemplos de programação com Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="52e3f-375">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
