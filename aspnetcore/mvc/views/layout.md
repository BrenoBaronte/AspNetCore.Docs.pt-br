---
title: Layout no ASP.NET Core
author: ardalis
description: Saiba como usar layouts comuns, compartilhar diretivas e executar um código comum antes de renderizar exibições em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/layout
ms.openlocfilehash: 502df268e7f5f33acfffccd5ec0bd65267fa12da
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060970"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="f4787-103">Layout no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4787-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="f4787-104">Por [Steve Smith](https://ardalis.com/) e [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="f4787-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="f4787-105">Páginas e exibições com frequência compartilham elementos visuais e programáticos.</span><span class="sxs-lookup"><span data-stu-id="f4787-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="f4787-106">Este artigo demonstra como:</span><span class="sxs-lookup"><span data-stu-id="f4787-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="f4787-107">Usar layouts comuns.</span><span class="sxs-lookup"><span data-stu-id="f4787-107">Use common layouts.</span></span>
* <span data-ttu-id="f4787-108">Compartilhar diretivas.</span><span class="sxs-lookup"><span data-stu-id="f4787-108">Share directives.</span></span>
* <span data-ttu-id="f4787-109">Executar o código comum antes de renderizar páginas ou modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="f4787-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="f4787-110">Este documento discute layouts para as duas abordagens diferentes para ASP.NET Core MVC: Razor páginas e controladores com exibições.</span><span class="sxs-lookup"><span data-stu-id="f4787-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="f4787-111">Para este tópico, as diferenças são mínimas:</span><span class="sxs-lookup"><span data-stu-id="f4787-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="f4787-112">Razor As páginas estão na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="f4787-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="f4787-113">Controladores com exibições usam uma pasta *Views* pasta exibições.</span><span class="sxs-lookup"><span data-stu-id="f4787-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="f4787-114">O que é um layout</span><span class="sxs-lookup"><span data-stu-id="f4787-114">What is a Layout</span></span>

<span data-ttu-id="f4787-115">A maioria dos aplicativos Web tem um layout comum que fornece aos usuários uma experiência consistente durante sua navegação de uma página a outra.</span><span class="sxs-lookup"><span data-stu-id="f4787-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="f4787-116">O layout normalmente inclui elementos comuns de interface do usuário, como o cabeçalho do aplicativo, elementos de menu ou de navegação e rodapé.</span><span class="sxs-lookup"><span data-stu-id="f4787-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Exemplo de layout da página](layout/_static/page-layout.png)

<span data-ttu-id="f4787-118">Estruturas HTML comuns, como scripts e folhas de estilo, também são usadas frequentemente por muitas páginas em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f4787-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="f4787-119">Todos esses elementos compartilhados podem ser definidos em um arquivo de *layout* , que pode ser referenciado por qualquer exibição usada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f4787-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="f4787-120">Os layouts reduzem o código duplicado nas exibições.</span><span class="sxs-lookup"><span data-stu-id="f4787-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="f4787-121">Por convenção, o layout padrão de um aplicativo ASP.NET Core é chamado *_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f4787-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml* .</span></span> <span data-ttu-id="f4787-122">Os arquivos de layout para novos projetos do ASP.NET Core criados com os modelos são:</span><span class="sxs-lookup"><span data-stu-id="f4787-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="f4787-123">Razor Páginas: *páginas/compartilhadas/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f4787-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![Pasta das páginas no Gerenciador de Soluções](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="f4787-125">Controlador com exibições: *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f4787-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![Pasta das exibições no Gerenciador de Soluções](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="f4787-127">O layout define um modelo de nível superior para exibições no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f4787-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="f4787-128">Aplicativos não exigem um layout.</span><span class="sxs-lookup"><span data-stu-id="f4787-128">Apps don't require a layout.</span></span> <span data-ttu-id="f4787-129">Os aplicativos podem definir mais de um layout, com diferentes exibições que especificam layouts diferentes.</span><span class="sxs-lookup"><span data-stu-id="f4787-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="f4787-130">O código a seguir mostra o arquivo de layout para um modelo de projeto criado com um controlador e exibições:</span><span class="sxs-lookup"><span data-stu-id="f4787-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="f4787-131">Especificando um layout</span><span class="sxs-lookup"><span data-stu-id="f4787-131">Specifying a Layout</span></span>

<span data-ttu-id="f4787-132">Razor as exibições têm uma `Layout` propriedade.</span><span class="sxs-lookup"><span data-stu-id="f4787-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="f4787-133">As exibições individuais especificam um layout com a configuração dessa propriedade:</span><span class="sxs-lookup"><span data-stu-id="f4787-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="f4787-134">O layout especificado pode usar um caminho completo (por exemplo, */Pages/Shared/_Layout.cshtml* ou */Views/Shared/_Layout.cshtml* ) ou um nome parcial (exemplo: `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="f4787-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml* ) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="f4787-135">Quando um nome parcial é fornecido, o Razor mecanismo de exibição pesquisa o arquivo de layout usando seu processo de descoberta padrão.</span><span class="sxs-lookup"><span data-stu-id="f4787-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="f4787-136">A pasta em que o método do manipulador (ou controlador) existe é pesquisada primeiro, seguida pela pasta *Shared* .</span><span class="sxs-lookup"><span data-stu-id="f4787-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="f4787-137">Esse processo de descoberta é idêntico àquele usado para descobrir [exibições parciais](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="f4787-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="f4787-138">Por padrão, todo layout precisa chamar `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="f4787-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="f4787-139">Sempre que a chamada a `RenderBody` for feita, o conteúdo da exibição será renderizado.</span><span class="sxs-lookup"><span data-stu-id="f4787-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="f4787-140">Seções</span><span class="sxs-lookup"><span data-stu-id="f4787-140">Sections</span></span>

<span data-ttu-id="f4787-141">Um layout, opcionalmente, pode referenciar uma ou mais *seções* , chamando `RenderSection`.</span><span class="sxs-lookup"><span data-stu-id="f4787-141">A layout can optionally reference one or more *sections* , by calling `RenderSection`.</span></span> <span data-ttu-id="f4787-142">As seções fornecem uma maneira de organizar o local em que determinados elementos da página devem ser colocados.</span><span class="sxs-lookup"><span data-stu-id="f4787-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="f4787-143">Cada chamada a `RenderSection` pode especificar se essa seção é obrigatória ou opcional:</span><span class="sxs-lookup"><span data-stu-id="f4787-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="f4787-144">Se uma seção obrigatória não for encontrada, uma exceção será gerada.</span><span class="sxs-lookup"><span data-stu-id="f4787-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="f4787-145">Modos de exibição individuais especificam o conteúdo a ser processado em uma seção usando a `@section` Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="f4787-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="f4787-146">Se uma página ou exibição definir uma seção, ela precisará ser renderizada (ou ocorrerá um erro).</span><span class="sxs-lookup"><span data-stu-id="f4787-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="f4787-147">Uma `@section` definição de exemplo no Razor modo de exibição páginas:</span><span class="sxs-lookup"><span data-stu-id="f4787-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="f4787-148">No código anterior, *scripts/main.js* é adicionado à seção `scripts` em uma página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="f4787-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="f4787-149">Outras páginas ou exibições no mesmo aplicativo podem não exigir esse script e não definirão uma seção de scripts.</span><span class="sxs-lookup"><span data-stu-id="f4787-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="f4787-150">A marcação a seguir usa o [Auxiliar de Marca Parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) para renderizar *_ValidationScriptsPartial.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f4787-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml* :</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="f4787-151">A marcação anterior foi gerada por [scaffolding Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="f4787-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="f4787-152">As seções definidas em uma página ou exibição estão disponíveis apenas em sua página de layout imediata.</span><span class="sxs-lookup"><span data-stu-id="f4787-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="f4787-153">Elas não podem ser referenciadas em parciais, componentes de exibição ou outras partes do sistema de exibição.</span><span class="sxs-lookup"><span data-stu-id="f4787-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="f4787-154">Ignorando seções</span><span class="sxs-lookup"><span data-stu-id="f4787-154">Ignoring sections</span></span>

<span data-ttu-id="f4787-155">Por padrão, o corpo e todas as seções de uma página de conteúdo precisam ser renderizados pela página de layout.</span><span class="sxs-lookup"><span data-stu-id="f4787-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="f4787-156">O Razor mecanismo de exibição impõe isso rastreando se o corpo e cada seção foram renderizados.</span><span class="sxs-lookup"><span data-stu-id="f4787-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="f4787-157">Para instruir o mecanismo de exibição a ignorar o corpo ou as seções, chame os métodos `IgnoreBody` e `IgnoreSection`.</span><span class="sxs-lookup"><span data-stu-id="f4787-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="f4787-158">O corpo e cada seção em uma Razor página devem ser renderizados ou ignorados.</span><span class="sxs-lookup"><span data-stu-id="f4787-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="f4787-159">Importando diretivas compartilhadas</span><span class="sxs-lookup"><span data-stu-id="f4787-159">Importing Shared Directives</span></span>

<span data-ttu-id="f4787-160">Exibições e páginas podem usar Razor diretivas para importar namespaces e usar [injeção de dependência](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="f4787-160">Views and pages can use Razor directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="f4787-161">Diretivas compartilhadas por diversas exibições podem ser especificadas em um arquivo *_ViewImports.cshtml* comum.</span><span class="sxs-lookup"><span data-stu-id="f4787-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f4787-162">O arquivo `_ViewImports` dá suporte às seguintes diretivas:</span><span class="sxs-lookup"><span data-stu-id="f4787-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="f4787-163">O arquivo não dá suporte a outros Razor recursos, como definições de funções e de seção.</span><span class="sxs-lookup"><span data-stu-id="f4787-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="f4787-164">Um arquivo `_ViewImports.cshtml` de exemplo:</span><span class="sxs-lookup"><span data-stu-id="f4787-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="f4787-165">O arquivo *_ViewImports.cshtml* para um aplicativo ASP.NET Core MVC normalmente é colocado na pasta *Pages* (ou *Views* ).</span><span class="sxs-lookup"><span data-stu-id="f4787-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views* ) folder.</span></span> <span data-ttu-id="f4787-166">Um arquivo *_ViewImports.cshtml* pode ser colocado em qualquer pasta, caso em que só será aplicado a páginas ou exibições nessa pasta e em suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="f4787-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="f4787-167">Arquivos `_ViewImports` são processados começando no nível raiz e, em seguida, para cada pasta até o local da página ou da exibição em si.</span><span class="sxs-lookup"><span data-stu-id="f4787-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="f4787-168">As configurações `_ViewImports` especificadas no nível raiz podem ser substituídas no nível da pasta.</span><span class="sxs-lookup"><span data-stu-id="f4787-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="f4787-169">Por exemplo, suponha:</span><span class="sxs-lookup"><span data-stu-id="f4787-169">For example, suppose:</span></span>

* <span data-ttu-id="f4787-170">O arquivo *_ViewImports.cshtml* no nível de raiz inclui `@model MyModel1` e `@addTagHelper *, MyTagHelper1`.</span><span class="sxs-lookup"><span data-stu-id="f4787-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="f4787-171">Um arquivo *_ViewImports.cshtml* de subpasta inclui `@model MyModel2` e `@addTagHelper *, MyTagHelper2`.</span><span class="sxs-lookup"><span data-stu-id="f4787-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="f4787-172">Páginas e exibições na subpasta terão acesso a Auxiliares de Marca e ao modelo `MyModel2`.</span><span class="sxs-lookup"><span data-stu-id="f4787-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="f4787-173">Se vários arquivos *_ViewImports.cshtml* forem encontrados na hierarquia de arquivos, o comportamento combinado das diretivas será:</span><span class="sxs-lookup"><span data-stu-id="f4787-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="f4787-174">`@addTagHelper`, `@removeTagHelper`: todos são executados, em ordem</span><span class="sxs-lookup"><span data-stu-id="f4787-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="f4787-175">`@tagHelperPrefix`: o mais próximo à exibição substitui todos os outros</span><span class="sxs-lookup"><span data-stu-id="f4787-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f4787-176">`@model`: o mais próximo à exibição substitui todos os outros</span><span class="sxs-lookup"><span data-stu-id="f4787-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f4787-177">`@inherits`: o mais próximo à exibição substitui todos os outros</span><span class="sxs-lookup"><span data-stu-id="f4787-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f4787-178">`@using`: todos são incluídos; duplicatas são ignoradas</span><span class="sxs-lookup"><span data-stu-id="f4787-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="f4787-179">`@inject`: para cada propriedade, o mais próximo à exibição substitui todos os outros com o mesmo nome de propriedade</span><span class="sxs-lookup"><span data-stu-id="f4787-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="f4787-180">Executando o código antes de cada exibição</span><span class="sxs-lookup"><span data-stu-id="f4787-180">Running Code Before Each View</span></span>

<span data-ttu-id="f4787-181">Código que precisa ser executado antes de cada exibição ou página precisa ser colocada no arquivo *_ViewStart.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f4787-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="f4787-182">Por convenção, o arquivo *_ViewStart.cshtml* está localizado na pasta *Pages* (ou *Views* ).</span><span class="sxs-lookup"><span data-stu-id="f4787-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views* ) folder.</span></span> <span data-ttu-id="f4787-183">As instruções listadas em *_ViewStart.cshtml* são executadas antes de cada exibição completa (não layouts nem exibições parciais).</span><span class="sxs-lookup"><span data-stu-id="f4787-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="f4787-184">Como [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* é hierárquico.</span><span class="sxs-lookup"><span data-stu-id="f4787-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="f4787-185">Se um arquivo *_ViewStart.cshtml* for definido na pasta de exibições ou páginas, ele será executado depois daquele definido na raiz da pasta *Pages* (ou *Views* ) (se houver).</span><span class="sxs-lookup"><span data-stu-id="f4787-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views* ) folder (if any).</span></span>

<span data-ttu-id="f4787-186">Um arquivo *_ViewStart.cshtml* de amostra:</span><span class="sxs-lookup"><span data-stu-id="f4787-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="f4787-187">O arquivo acima especifica que todas as exibições usarão o layout *_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f4787-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="f4787-188">*_ViewStart.cshtml* é *_ViewImports.cshtml* normalmente **não** são colocados na pasta */Pages/Shared* (ou */Views/Shared* ).</span><span class="sxs-lookup"><span data-stu-id="f4787-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared* ) folder.</span></span> <span data-ttu-id="f4787-189">As versões no nível do aplicativo desses arquivos devem ser colocadas diretamente na pasta */Pages* (ou */Views* ).</span><span class="sxs-lookup"><span data-stu-id="f4787-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views* ) folder.</span></span>
