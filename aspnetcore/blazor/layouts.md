---
title: :::no-loc(Blazor):::Layouts de ASP.NET Core
author: guardrex
description: 'Saiba como criar componentes de layout reutilizáveis para :::no-loc(Blazor)::: aplicativos.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/layouts
ms.openlocfilehash: e61c76b5d53ad7646961632d00b047ecd2d9e477
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055601"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="63f26-103">:::no-loc(Blazor):::Layouts de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63f26-103">ASP.NET Core :::no-loc(Blazor)::: layouts</span></span>

<span data-ttu-id="63f26-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="63f26-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="63f26-105">Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="63f26-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="63f26-106">Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem eficiente.</span><span class="sxs-lookup"><span data-stu-id="63f26-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="63f26-107">Toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="63f26-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="63f26-108">Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="63f26-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="63f26-109">Os *layouts* resolvem esse problema.</span><span class="sxs-lookup"><span data-stu-id="63f26-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="63f26-110">Tecnicamente, um layout é apenas outro componente.</span><span class="sxs-lookup"><span data-stu-id="63f26-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="63f26-111">Um layout é definido em um :::no-loc(Razor)::: modelo ou em código C# e pode usar [Associação de dados](xref:blazor/components/data-binding), [injeção de dependência](xref:blazor/fundamentals/dependency-injection)e outros cenários de componente.</span><span class="sxs-lookup"><span data-stu-id="63f26-111">A layout is defined in a :::no-loc(Razor)::: template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="63f26-112">Para transformar um *componente* em um *layout* , o componente:</span><span class="sxs-lookup"><span data-stu-id="63f26-112">To turn a *component* into a *layout* , the component:</span></span>

* <span data-ttu-id="63f26-113">Herda de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , que define uma <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriedade para o conteúdo renderizado dentro do layout.</span><span class="sxs-lookup"><span data-stu-id="63f26-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="63f26-114">Usa a :::no-loc(Razor)::: sintaxe `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.</span><span class="sxs-lookup"><span data-stu-id="63f26-114">Uses the :::no-loc(Razor)::: syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="63f26-115">O exemplo de código a seguir mostra o :::no-loc(Razor)::: modelo de um componente de layout, `MainLayout.razor` .</span><span class="sxs-lookup"><span data-stu-id="63f26-115">The following code sample shows the :::no-loc(Razor)::: template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="63f26-116">O layout herda <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> e define o `@Body` entre a barra de navegação e o rodapé:</span><span class="sxs-lookup"><span data-stu-id="63f26-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="mainlayout-component"></a><span data-ttu-id="63f26-117">componente `MainLayout`</span><span class="sxs-lookup"><span data-stu-id="63f26-117">`MainLayout` component</span></span>

<span data-ttu-id="63f26-118">Em um aplicativo baseado em um dos :::no-loc(Blazor)::: modelos de projeto, o `MainLayout` componente ( `MainLayout.razor` ) está na pasta do aplicativo `Shared` :</span><span class="sxs-lookup"><span data-stu-id="63f26-118">In an app based on one of the :::no-loc(Blazor)::: project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

[!code-razor[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Shared/MainLayout.razor)]

## <a name="default-layout"></a><span data-ttu-id="63f26-119">Definir layout</span><span class="sxs-lookup"><span data-stu-id="63f26-119">Default layout</span></span>

<span data-ttu-id="63f26-120">Especifique o layout do aplicativo padrão no <xref:Microsoft.AspNetCore.Components.Routing.Router> componente no arquivo do aplicativo `App.razor` .</span><span class="sxs-lookup"><span data-stu-id="63f26-120">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="63f26-121">O componente a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router> , que é fornecido pelos :::no-loc(Blazor)::: modelos padrão, define o layout padrão para o `MainLayout` componente:</span><span class="sxs-lookup"><span data-stu-id="63f26-121">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default :::no-loc(Blazor)::: templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="63f26-122">Para fornecer um layout padrão para <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, especifique um <xref:Microsoft.AspNetCore.Components.LayoutView> para o <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo:</span><span class="sxs-lookup"><span data-stu-id="63f26-122">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="63f26-123">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente, consulte <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="63f26-123">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="63f26-124">A especificação do layout como um layout padrão no roteador é uma prática útil, pois pode ser substituída por componente ou por pasta.</span><span class="sxs-lookup"><span data-stu-id="63f26-124">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="63f26-125">Prefira usar o roteador para definir o layout padrão do aplicativo porque ele é a técnica mais geral.</span><span class="sxs-lookup"><span data-stu-id="63f26-125">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="63f26-126">Especificar um layout em um componente</span><span class="sxs-lookup"><span data-stu-id="63f26-126">Specify a layout in a component</span></span>

<span data-ttu-id="63f26-127">Use a :::no-loc(Razor)::: diretiva `@layout` para aplicar um layout a um componente.</span><span class="sxs-lookup"><span data-stu-id="63f26-127">Use the :::no-loc(Razor)::: directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="63f26-128">O compilador converte `@layout` em um <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , que é aplicado à classe de componente.</span><span class="sxs-lookup"><span data-stu-id="63f26-128">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="63f26-129">O conteúdo do componente a seguir `MasterList` é inserido no `MasterLayout` na posição de `@Body` :</span><span class="sxs-lookup"><span data-stu-id="63f26-129">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="63f26-130">Especificar o layout diretamente em um componente substitui um *Layout padrão* definido no roteador ou uma `@layout` diretiva importada do `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="63f26-130">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="63f26-131">Seleção de layout centralizado</span><span class="sxs-lookup"><span data-stu-id="63f26-131">Centralized layout selection</span></span>

<span data-ttu-id="63f26-132">Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="63f26-132">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="63f26-133">O compilador inclui as diretivas especificadas no arquivo Imports em todos os :::no-loc(Razor)::: modelos na mesma pasta e recursivamente em todas as suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="63f26-133">The compiler includes the directives specified in the imports file in all of the :::no-loc(Razor)::: templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="63f26-134">Portanto, um `_Imports.razor` arquivo que contém `@layout MyCoolLayout` garante que todos os componentes em uma pasta usem o `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="63f26-134">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="63f26-135">Não há necessidade de adicionar repetidamente `@layout MyCoolLayout` a todos os `.razor` arquivos dentro da pasta e subpastas.</span><span class="sxs-lookup"><span data-stu-id="63f26-135">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="63f26-136">`@using` as diretivas também são aplicadas aos componentes da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="63f26-136">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="63f26-137">As seguintes `_Imports.razor` importações de arquivo:</span><span class="sxs-lookup"><span data-stu-id="63f26-137">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="63f26-138">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="63f26-138">`MyCoolLayout`.</span></span>
* <span data-ttu-id="63f26-139">Todos os :::no-loc(Razor)::: componentes na mesma pasta e em todas as subpastas.</span><span class="sxs-lookup"><span data-stu-id="63f26-139">All :::no-loc(Razor)::: components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="63f26-140">O namespace `:::no-loc(Blazor):::App1.Data`.</span><span class="sxs-lookup"><span data-stu-id="63f26-140">The `:::no-loc(Blazor):::App1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="63f26-141">O `_Imports.razor` arquivo é semelhante ao [arquivo _ViewImports. cshtml para :::no-loc(Razor)::: exibições e páginas](xref:mvc/views/layout#importing-shared-directives) , mas é aplicado especificamente a :::no-loc(Razor)::: arquivos de componente.</span><span class="sxs-lookup"><span data-stu-id="63f26-141">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for :::no-loc(Razor)::: views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to :::no-loc(Razor)::: component files.</span></span>

<span data-ttu-id="63f26-142">A especificação de um layout em `_Imports.razor` substitui um layout especificado como o *Layout padrão* do roteador.</span><span class="sxs-lookup"><span data-stu-id="63f26-142">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout* .</span></span>

> [!WARNING]
> <span data-ttu-id="63f26-143">**Não** adicione uma :::no-loc(Razor)::: `@layout` diretiva ao `_Imports.razor` arquivo raiz, o que resulta em um loop infinito de layouts no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="63f26-143">Do **not** add a :::no-loc(Razor)::: `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="63f26-144">Para controlar o layout do aplicativo padrão, especifique o layout no `Router` componente.</span><span class="sxs-lookup"><span data-stu-id="63f26-144">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="63f26-145">Para obter mais informações, consulte a seção [Layout padrão](#default-layout) .</span><span class="sxs-lookup"><span data-stu-id="63f26-145">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="63f26-146">Layouts aninhados</span><span class="sxs-lookup"><span data-stu-id="63f26-146">Nested layouts</span></span>

<span data-ttu-id="63f26-147">Os aplicativos podem consistir em layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="63f26-147">Apps can consist of nested layouts.</span></span> <span data-ttu-id="63f26-148">Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout.</span><span class="sxs-lookup"><span data-stu-id="63f26-148">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="63f26-149">Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.</span><span class="sxs-lookup"><span data-stu-id="63f26-149">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="63f26-150">O exemplo a seguir mostra como usar layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="63f26-150">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="63f26-151">O `EpisodesComponent.razor` arquivo é o componente a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="63f26-151">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="63f26-152">O componente faz referência a `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="63f26-152">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="63f26-153">O `MasterListLayout.razor` arquivo fornece o `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="63f26-153">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="63f26-154">O layout faz referência a outro layout, `MasterLayout` , onde é renderizado.</span><span class="sxs-lookup"><span data-stu-id="63f26-154">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="63f26-155">`EpisodesComponent` é renderizado onde `@Body` aparece:</span><span class="sxs-lookup"><span data-stu-id="63f26-155">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="63f26-156">Por fim, `MasterLayout` no `MasterLayout.razor` contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé.</span><span class="sxs-lookup"><span data-stu-id="63f26-156">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="63f26-157">`MasterListLayout` com o `EpisodesComponent` é renderizado onde `@Body` aparece:</span><span class="sxs-lookup"><span data-stu-id="63f26-157">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="63f26-158">Compartilhar um :::no-loc(Razor)::: layout de páginas com componentes integrados</span><span class="sxs-lookup"><span data-stu-id="63f26-158">Share a :::no-loc(Razor)::: Pages layout with integrated components</span></span>

<span data-ttu-id="63f26-159">Quando componentes roteáveis são integrados a um :::no-loc(Razor)::: aplicativo de páginas, o layout compartilhado do aplicativo pode ser usado com os componentes.</span><span class="sxs-lookup"><span data-stu-id="63f26-159">When routable components are integrated into a :::no-loc(Razor)::: Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="63f26-160">Para obter mais informações, consulte <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="63f26-160">For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63f26-161">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="63f26-161">Additional resources</span></span>

* <xref:mvc/views/layout>
