---
title: BlazorIsolamento ASP.NET Core CSS
author: daveabrock
description: Saiba como o isolamento CSS permite que você alcance o CSS para seus componentes, o que pode simplificar seu CSS e evitar colisões com outros componentes ou bibliotecas.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529976"
---
# <a name="aspnet-core-blazor-css-isolation"></a><span data-ttu-id="06d38-103">BlazorIsolamento ASP.NET Core CSS</span><span class="sxs-lookup"><span data-stu-id="06d38-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="06d38-104">Por [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="06d38-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="06d38-105">O isolamento CSS simplifica a superfície CSS de um aplicativo, impedindo dependências em estilos globais e ajuda a evitar conflitos de estilo entre componentes e bibliotecas.</span><span class="sxs-lookup"><span data-stu-id="06d38-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="06d38-106">Habilitar isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="06d38-106">Enable CSS isolation</span></span> 

<span data-ttu-id="06d38-107">Para definir estilos específicos do componente, crie um `.razor.css` arquivo que corresponda ao nome do `.razor` arquivo para o componente na mesma pasta.</span><span class="sxs-lookup"><span data-stu-id="06d38-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component in the same folder.</span></span> <span data-ttu-id="06d38-108">O `.razor.css` arquivo é um *arquivo CSS com escopo*.</span><span class="sxs-lookup"><span data-stu-id="06d38-108">The `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="06d38-109">Para um `Example` componente em um `Example.razor` arquivo, crie um arquivo junto com o componente chamado `Example.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="06d38-109">For an `Example` component in an `Example.razor` file, create a file alongside the component named `Example.razor.css`.</span></span> <span data-ttu-id="06d38-110">O `Example.razor.css` arquivo deve residir na mesma pasta que o `Example` componente ( `Example.razor` ).</span><span class="sxs-lookup"><span data-stu-id="06d38-110">The `Example.razor.css` file must reside in the same folder as the `Example` component (`Example.razor`).</span></span> <span data-ttu-id="06d38-111">O `Example` nome base do arquivo "" **não** diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="06d38-111">The "`Example`" base name of the file is **not** case-sensitive.</span></span>

<span data-ttu-id="06d38-112">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="06d38-112">`Pages/Example.razor`:</span></span>

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

<span data-ttu-id="06d38-113">`Pages/Example.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="06d38-113">`Pages/Example.razor.css`:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="06d38-114">**Os estilos definidos em `Example.razor.css` são aplicados somente à saída renderizada do `Example` componente.**</span><span class="sxs-lookup"><span data-stu-id="06d38-114">**The styles defined in `Example.razor.css` are only applied to the rendered output of the `Example` component.**</span></span> <span data-ttu-id="06d38-115">O isolamento de CSS é aplicado aos elementos HTML no Razor arquivo correspondente.</span><span class="sxs-lookup"><span data-stu-id="06d38-115">CSS isolation is applied to HTML elements in the matching Razor file.</span></span> <span data-ttu-id="06d38-116">Quaisquer `h1` declarações CSS definidas em outro lugar no aplicativo não entram em conflito com os `Example` estilos do componente.</span><span class="sxs-lookup"><span data-stu-id="06d38-116">Any `h1` CSS declarations defined elsewhere in the app don't conflict with the `Example` component's styles.</span></span>

> [!NOTE]
> <span data-ttu-id="06d38-117">Para garantir o isolamento de estilo quando ocorre o agrupamento, não há suporte para a importação de CSS em Razor blocos de código.</span><span class="sxs-lookup"><span data-stu-id="06d38-117">In order to guarantee style isolation when bundling occurs, importing CSS in Razor code blocks isn't supported.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="06d38-118">Agrupamento de isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="06d38-118">CSS isolation bundling</span></span>

<span data-ttu-id="06d38-119">O isolamento de CSS ocorre no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="06d38-119">CSS isolation occurs at build time.</span></span> <span data-ttu-id="06d38-120">Durante esse processo, o Blazor reescreve seletores CSS para corresponder à marcação renderizada pelo componente.</span><span class="sxs-lookup"><span data-stu-id="06d38-120">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="06d38-121">Esses estilos CSS reescritos são agrupados e produzidos como um ativo estático em `{PROJECT NAME}.styles.css` , em que o espaço reservado `{PROJECT NAME}` é o nome do produto ou do pacote referenciado.</span><span class="sxs-lookup"><span data-stu-id="06d38-121">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="06d38-122">Por padrão, esses arquivos estáticos são referenciados no caminho raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="06d38-122">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="06d38-123">No aplicativo, referencie o arquivo agrupado inspecionando a referência dentro da `<head>` marca do HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="06d38-123">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="06d38-124">No arquivo empacotado, cada componente é associado a um identificador de escopo.</span><span class="sxs-lookup"><span data-stu-id="06d38-124">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="06d38-125">Para cada componente com estilo, um atributo HTML é anexado com o formato `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="06d38-125">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="06d38-126">O identificador é exclusivo e diferente para cada aplicativo.</span><span class="sxs-lookup"><span data-stu-id="06d38-126">The identifier is unique and different for each app.</span></span> <span data-ttu-id="06d38-127">No componente renderizado `Counter` , Blazor acrescenta um identificador de escopo ao `h1` elemento:</span><span class="sxs-lookup"><span data-stu-id="06d38-127">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="06d38-128">O `ProjectName.styles.css` arquivo usa o identificador de escopo para agrupar uma declaração de estilo com seu componente.</span><span class="sxs-lookup"><span data-stu-id="06d38-128">The `ProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="06d38-129">O exemplo a seguir fornece o estilo para o `<h1>` elemento anterior:</span><span class="sxs-lookup"><span data-stu-id="06d38-129">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="06d38-130">No momento da compilação, um pacote de projeto é criado com a Convenção `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` , em que o espaço reservado `{STATIC WEB ASSETS BASE PATH}` é o caminho base dos ativos da Web estáticos.</span><span class="sxs-lookup"><span data-stu-id="06d38-130">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="06d38-131">Se outros projetos forem utilizados, como pacotes NuGet ou bibliotecas de [ Razor classes](xref:blazor/components/class-libraries), o arquivo agrupado:</span><span class="sxs-lookup"><span data-stu-id="06d38-131">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="06d38-132">Faz referência aos estilos usando importações CSS.</span><span class="sxs-lookup"><span data-stu-id="06d38-132">References the styles using CSS imports.</span></span>
* <span data-ttu-id="06d38-133">Não é publicado como um ativo da Web estático do aplicativo que consome os estilos.</span><span class="sxs-lookup"><span data-stu-id="06d38-133">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="06d38-134">Suporte a componentes filho</span><span class="sxs-lookup"><span data-stu-id="06d38-134">Child component support</span></span>

<span data-ttu-id="06d38-135">Por padrão, o isolamento de CSS só se aplica ao componente que você associa ao formato `{COMPONENT NAME}.razor.css` , em que o espaço reservado `{COMPONENT NAME}` é geralmente o nome do componente.</span><span class="sxs-lookup"><span data-stu-id="06d38-135">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="06d38-136">Para aplicar as alterações a um componente filho, use o `::deep` combinador para quaisquer elementos descendentes no arquivo do componente pai `.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="06d38-136">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="06d38-137">O `::deep` combinador seleciona elementos que são *descendentes* do identificador de escopo gerado de um elemento.</span><span class="sxs-lookup"><span data-stu-id="06d38-137">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="06d38-138">O exemplo a seguir mostra um componente pai chamado `Parent` com um componente filho chamado `Child` .</span><span class="sxs-lookup"><span data-stu-id="06d38-138">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="06d38-139">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="06d38-139">`Pages/Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="06d38-140">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="06d38-140">`Shared/Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="06d38-141">Atualize a `h1` declaração em `Parent.razor.css` com o `::deep` combinador para significar que a `h1` declaração de estilo deve ser aplicada ao componente pai e a seus filhos.</span><span class="sxs-lookup"><span data-stu-id="06d38-141">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children.</span></span>

<span data-ttu-id="06d38-142">`Pages/Parent.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="06d38-142">`Pages/Parent.razor.css`:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="06d38-143">O `h1` estilo agora se aplica aos `Parent` `Child` componentes e sem a necessidade de criar um arquivo CSS com escopo separado para o componente filho.</span><span class="sxs-lookup"><span data-stu-id="06d38-143">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

<span data-ttu-id="06d38-144">O `::deep` combinador funciona apenas com elementos descendentes.</span><span class="sxs-lookup"><span data-stu-id="06d38-144">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="06d38-145">A marcação a seguir aplica os `h1` estilos aos componentes conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="06d38-145">The following markup applies the `h1` styles to components as expected.</span></span> <span data-ttu-id="06d38-146">O identificador de escopo do componente pai é aplicado ao `div` elemento, portanto, o navegador sabe herdar estilos do componente pai.</span><span class="sxs-lookup"><span data-stu-id="06d38-146">The parent component's scope identifier is applied to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>

<span data-ttu-id="06d38-147">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="06d38-147">`Pages/Parent.razor`:</span></span>

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

<span data-ttu-id="06d38-148">No entanto, a exclusão do `div` elemento remove a relação descendente.</span><span class="sxs-lookup"><span data-stu-id="06d38-148">However, excluding the `div` element removes the descendant relationship.</span></span> <span data-ttu-id="06d38-149">No exemplo a seguir, o estilo **não** é aplicado ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="06d38-149">In the following example, the style is **not** applied to the child component.</span></span>

<span data-ttu-id="06d38-150">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="06d38-150">`Pages/Parent.razor`:</span></span>

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a><span data-ttu-id="06d38-151">Suporte ao pré-processador do CSS</span><span class="sxs-lookup"><span data-stu-id="06d38-151">CSS preprocessor support</span></span>

<span data-ttu-id="06d38-152">Os pré-processadores CSS são úteis para melhorar o desenvolvimento de CSS utilizando recursos como variáveis, aninhamento, módulos, mesclas e herança.</span><span class="sxs-lookup"><span data-stu-id="06d38-152">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="06d38-153">Embora o isolamento CSS não dê suporte nativo a pré-processadores CSS, como o Sass ou menos, a integração de pré-processadores CSS é direta, desde que a compilação do pré-processador ocorra antes de Blazor regravar os seletores CSS durante o processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="06d38-153">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="06d38-154">Usando o Visual Studio, por exemplo, configure a compilação de pré-processador existente como uma tarefa de **compilação anterior** no Gerenciador de executores de tarefas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="06d38-154">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="06d38-155">Muitos pacotes NuGet de terceiros, como o [delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), podem COMPILAR arquivos Sass/SCSS no início do processo de compilação antes que o isolamento de CSS ocorra, e nenhuma configuração adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="06d38-155">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="06d38-156">Configuração de isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="06d38-156">CSS isolation configuration</span></span>

<span data-ttu-id="06d38-157">O isolamento de CSS é projetado para funcionar de forma integrada, mas fornece configuração para alguns cenários avançados, como quando há dependências em ferramentas ou fluxos de trabalho existentes.</span><span class="sxs-lookup"><span data-stu-id="06d38-157">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="06d38-158">Personalizar o formato do identificador de escopo</span><span class="sxs-lookup"><span data-stu-id="06d38-158">Customize scope identifier format</span></span>

<span data-ttu-id="06d38-159">Por padrão, os identificadores de escopo usam o formato `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="06d38-159">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="06d38-160">Para personalizar o formato do identificador de escopo, atualize o arquivo de projeto para um padrão desejado:</span><span class="sxs-lookup"><span data-stu-id="06d38-160">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="06d38-161">No exemplo anterior, o CSS gerado para `Example.Razor.css` altera seu identificador de escopo de `b-<10-character-string>` para `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="06d38-161">In the preceding example, the CSS generated for `Example.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

<span data-ttu-id="06d38-162">Use identificadores de escopo para obter herança com arquivos CSS com escopo definido.</span><span class="sxs-lookup"><span data-stu-id="06d38-162">Use scope identifiers to achieve inheritance with scoped CSS files.</span></span> <span data-ttu-id="06d38-163">No exemplo de arquivo de projeto a seguir, um `BaseComponent.razor.css` arquivo contém estilos comuns entre componentes.</span><span class="sxs-lookup"><span data-stu-id="06d38-163">In the following project file example, a `BaseComponent.razor.css` file contains common styles across components.</span></span> <span data-ttu-id="06d38-164">Um `DerivedComponent.razor.css` arquivo herda esses estilos.</span><span class="sxs-lookup"><span data-stu-id="06d38-164">A `DerivedComponent.razor.css` file inherits these styles.</span></span>

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="06d38-165">Use o operador curinga ( `*` ) para compartilhar identificadores de escopo entre vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="06d38-165">Use the wildcard (`*`) operator to share scope identifiers across multiple files:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="06d38-166">Alterar o caminho base para ativos da Web estáticos</span><span class="sxs-lookup"><span data-stu-id="06d38-166">Change base path for static web assets</span></span>

<span data-ttu-id="06d38-167">O `scoped.styles.css` arquivo é gerado na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="06d38-167">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="06d38-168">No arquivo de projeto, use a `StaticWebAssetBasePath` propriedade para alterar o caminho padrão.</span><span class="sxs-lookup"><span data-stu-id="06d38-168">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="06d38-169">O exemplo a seguir coloca o `scoped.styles.css` arquivo e o restante dos ativos do aplicativo, no `_content` caminho:</span><span class="sxs-lookup"><span data-stu-id="06d38-169">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="06d38-170">Desabilitar o agrupamento automático</span><span class="sxs-lookup"><span data-stu-id="06d38-170">Disable automatic bundling</span></span>

<span data-ttu-id="06d38-171">Para recusar a forma como Blazor o publica e carrega arquivos com escopo em tempo de execução, use a `DisableScopedCssBundling` propriedade.</span><span class="sxs-lookup"><span data-stu-id="06d38-171">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="06d38-172">Ao usar essa propriedade, isso significa que outras ferramentas ou processos são responsáveis por pegar os arquivos CSS isolados do `obj` diretório e publicá-los e carregá-los em tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="06d38-172">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a><span data-ttu-id="06d38-173">Razor suporte à biblioteca de classes (RCL)</span><span class="sxs-lookup"><span data-stu-id="06d38-173">Razor class library (RCL) support</span></span>

<span data-ttu-id="06d38-174">Quando uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) fornece estilos isolados, o `<link>` atributo da marca `href` aponta para `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` , onde os espaços reservados são:</span><span class="sxs-lookup"><span data-stu-id="06d38-174">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="06d38-175">`{STATIC WEB ASSET BASE PATH}`: O caminho base do ativo da Web estático.</span><span class="sxs-lookup"><span data-stu-id="06d38-175">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="06d38-176">`{ASSEMBLY NAME}`: O nome do assembly da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="06d38-176">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="06d38-177">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="06d38-177">In the following example:</span></span>

* <span data-ttu-id="06d38-178">O caminho base do ativo da Web estático é `_content/ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="06d38-178">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="06d38-179">O nome do assembly da biblioteca de classes é `ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="06d38-179">The class library's assembly name is `ClassLib`.</span></span>

<span data-ttu-id="06d38-180">`wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="06d38-180">`wwwroot/index.html` (Blazor WebAssembly) or `Pages_Host.cshtml` (Blazor Server):</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="06d38-181">Para obter mais informações sobre RCLs e bibliotecas de componentes, consulte:</span><span class="sxs-lookup"><span data-stu-id="06d38-181">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="06d38-182"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="06d38-182"><xref:blazor/components/class-libraries>.</span></span>
