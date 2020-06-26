---
title: Usar ASP.NET Core APIs em uma biblioteca de classes
author: scottaddie
description: Saiba como usar ASP.NET Core APIs em uma biblioteca de classes.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 1c794092b856a916a318956d7cfb357d46a22d1d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399641"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="51cd4-103">Usar ASP.NET Core APIs em uma biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="51cd4-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="51cd4-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="51cd4-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="51cd4-105">Este documento fornece diretrizes para usar ASP.NET Core APIs em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="51cd4-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="51cd4-106">Para todas as outras diretrizes de biblioteca, consulte [diretrizes de biblioteca](/dotnet/standard/library-guidance/)de software livre.</span><span class="sxs-lookup"><span data-stu-id="51cd4-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="51cd4-107">Determinar quais versões de ASP.NET Core para dar suporte</span><span class="sxs-lookup"><span data-stu-id="51cd4-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="51cd4-108">ASP.NET Core adere à política de [suporte do .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="51cd4-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="51cd4-109">Consulte a política de suporte ao determinar a quais ASP.NET Core versões dar suporte em uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="51cd4-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="51cd4-110">Uma biblioteca deve:</span><span class="sxs-lookup"><span data-stu-id="51cd4-110">A library should:</span></span>

* <span data-ttu-id="51cd4-111">Tome um esforço para dar suporte a todas as versões ASP.NET Core classificadas como *suporte a longo prazo* (LTS).</span><span class="sxs-lookup"><span data-stu-id="51cd4-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="51cd4-112">Não se sinta obrigado a dar suporte a ASP.NET Core versões classificadas como *fim da vida útil* (EOL).</span><span class="sxs-lookup"><span data-stu-id="51cd4-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="51cd4-113">À medida que as versões de visualização de ASP.NET Core são disponibilizadas, as alterações significativas são postadas no repositório GitHub [ASPNET/comunicados](https://github.com/aspnet/Announcements/issues) .</span><span class="sxs-lookup"><span data-stu-id="51cd4-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="51cd4-114">O teste de compatibilidade das bibliotecas pode ser conduzido conforme os recursos da estrutura estão sendo desenvolvidos.</span><span class="sxs-lookup"><span data-stu-id="51cd4-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="51cd4-115">Usar a estrutura compartilhada ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51cd4-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="51cd4-116">Com o lançamento do .NET Core 3,0, muitos ASP.NET Core assemblies não são mais publicados no NuGet como pacotes.</span><span class="sxs-lookup"><span data-stu-id="51cd4-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="51cd4-117">Em vez disso, os assemblies são incluídos na `Microsoft.AspNetCore.App` estrutura compartilhada, que é instalada com os instaladores de SDK do .NET Core e tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="51cd4-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="51cd4-118">Para obter uma lista de pacotes que não estão mais sendo publicados, consulte [remover referências de pacotes obsoletos](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="51cd4-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="51cd4-119">A partir do .NET Core 3,0, os projetos que usam o `Microsoft.NET.Sdk.Web` SDK do MSBuild referenciam implicitamente a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="51cd4-120">Os projetos que usam o `Microsoft.NET.Sdk` SDK do ou do `Microsoft.NET.Sdk.Razor` devem referenciar ASP.NET Core usar ASP.NET Core APIs na estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="51cd4-121">Para fazer referência a ASP.NET Core, adicione o seguinte `<FrameworkReference>` elemento ao seu arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="51cd4-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="51cd4-122">A referência a ASP.NET Core dessa maneira só tem suporte em projetos destinados ao .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="51cd4-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="51cd4-123">Incluir Blazor extensibilidade</span><span class="sxs-lookup"><span data-stu-id="51cd4-123">Include Blazor extensibility</span></span>

Blazor<span data-ttu-id="51cd4-124">dá suporte ao Webassembly (WASM) e [modelos de hospedagem](xref:blazor/hosting-models)de servidor.</span><span class="sxs-lookup"><span data-stu-id="51cd4-124"> supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="51cd4-125">A menos que haja um motivo específico para não, uma biblioteca de [ Razor componentes](xref:blazor/components/index) deve dar suporte a ambos os modelos de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="51cd4-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="51cd4-126">Uma Razor biblioteca de componentes deve usar o [Microsoft. net. Sdk. Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="51cd4-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="51cd4-127">Suporte para ambos os modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="51cd4-127">Support both hosting models</span></span>

<span data-ttu-id="51cd4-128">Para dar suporte ao Razor consumo de componentes de [Blazor Server](xref:blazor/hosting-models#blazor-server) projetos e [ Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) , use as instruções a seguir para o seu editor.</span><span class="sxs-lookup"><span data-stu-id="51cd4-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="51cd4-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51cd4-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="51cd4-130">Use o modelo de projeto de \*\* Razor biblioteca de classes\*\* .</span><span class="sxs-lookup"><span data-stu-id="51cd4-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="51cd4-131">A caixa de seleção páginas de suporte do modelo **e exibições** deve ser desmarcada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="51cd4-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51cd4-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="51cd4-133">Execute o seguinte comando no terminal integrado:</span><span class="sxs-lookup"><span data-stu-id="51cd4-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="51cd4-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="51cd4-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="51cd4-135">Use o modelo de projeto de \*\* Razor biblioteca de classes\*\* .</span><span class="sxs-lookup"><span data-stu-id="51cd4-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="51cd4-136">O projeto gerado a partir do modelo faz o seguinte:</span><span class="sxs-lookup"><span data-stu-id="51cd4-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="51cd4-137">Destinos .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="51cd4-138">Define a propriedade `RazorLangVersion` como `3.0`.</span><span class="sxs-lookup"><span data-stu-id="51cd4-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="51cd4-139">`3.0`é o valor padrão para o .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="51cd4-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="51cd4-140">Adiciona as seguintes referências de pacote:</span><span class="sxs-lookup"><span data-stu-id="51cd4-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="51cd4-141">Microsoft. AspNetCore. Components</span><span class="sxs-lookup"><span data-stu-id="51cd4-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="51cd4-142">Microsoft. AspNetCore. Components. Web</span><span class="sxs-lookup"><span data-stu-id="51cd4-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="51cd4-143">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="51cd4-144">Suporte a um modelo de hospedagem específico</span><span class="sxs-lookup"><span data-stu-id="51cd4-144">Support a specific hosting model</span></span>

<span data-ttu-id="51cd4-145">É muito menos comum oferecer suporte a um único Blazor modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="51cd4-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="51cd4-146">Por exemplo, para dar suporte ao Razor consumo de componentes [Blazor Server](xref:blazor/hosting-models#blazor-server) somente de projetos:</span><span class="sxs-lookup"><span data-stu-id="51cd4-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="51cd4-147">Direcione o .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="51cd4-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="51cd4-148">Adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="51cd4-149">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="51cd4-150">Para obter mais informações sobre bibliotecas que contêm Razor componentes, consulte [bibliotecas de classes de Razor componentes de ASP.NET Core](xref:blazor/components/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="51cd4-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/components/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="51cd4-151">Incluir extensibilidade MVC</span><span class="sxs-lookup"><span data-stu-id="51cd4-151">Include MVC extensibility</span></span>

<span data-ttu-id="51cd4-152">Esta seção descreve as recomendações para bibliotecas que incluem:</span><span class="sxs-lookup"><span data-stu-id="51cd4-152">This section outlines recommendations for libraries that include:</span></span>

* <span data-ttu-id="51cd4-153">[Razorexibições ou Razor páginas](#razor-views-or-razor-pages)</span><span class="sxs-lookup"><span data-stu-id="51cd4-153">[Razor views or Razor Pages](#razor-views-or-razor-pages)</span></span>
* [<span data-ttu-id="51cd4-154">Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="51cd4-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="51cd4-155">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="51cd4-155">View components</span></span>](#view-components)

<span data-ttu-id="51cd4-156">Esta seção não aborda vários destinos para dar suporte a várias versões do MVC.</span><span class="sxs-lookup"><span data-stu-id="51cd4-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="51cd4-157">Para obter orientação sobre como dar suporte a várias versões de ASP.NET Core, consulte [suporte para várias versões de ASP.NET Core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="51cd4-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a>Razor<span data-ttu-id="51cd4-158">exibições ou Razor páginas</span><span class="sxs-lookup"><span data-stu-id="51cd4-158"> views or Razor Pages</span></span>

<span data-ttu-id="51cd4-159">Um projeto que inclui [ Razor exibições](xref:mvc/views/overview) ou [ Razor páginas](xref:razor-pages/index) deve usar o [Microsoft. net. Sdk. Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="51cd4-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="51cd4-160">Se o projeto for destinado ao .NET Core 3. x, ele exigirá:</span><span class="sxs-lookup"><span data-stu-id="51cd4-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="51cd4-161">Uma `AddRazorSupportForMvc` Propriedade do MSBuild definida como `true` .</span><span class="sxs-lookup"><span data-stu-id="51cd4-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="51cd4-162">Um `<FrameworkReference>` elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="51cd4-163">O modelo de projeto de \*\* Razor biblioteca de classes\*\* atende aos requisitos anteriores para projetos que visam o .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="51cd4-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="51cd4-164">Use as instruções a seguir para o seu editor.</span><span class="sxs-lookup"><span data-stu-id="51cd4-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="51cd4-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51cd4-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="51cd4-166">Use o modelo de projeto de \*\* Razor biblioteca de classes\*\* .</span><span class="sxs-lookup"><span data-stu-id="51cd4-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="51cd4-167">A caixa de seleção páginas de suporte do modelo **e exibições** deve ser selecionada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="51cd4-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51cd4-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="51cd4-169">Execute o seguinte comando no terminal integrado:</span><span class="sxs-lookup"><span data-stu-id="51cd4-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="51cd4-170">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="51cd4-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="51cd4-171">Não há suporte ao modelo de projeto no momento.</span><span class="sxs-lookup"><span data-stu-id="51cd4-171">No project template support at this time.</span></span>

---

<span data-ttu-id="51cd4-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="51cd4-173">Se o projeto for direcionado .NET Standard em vez disso, uma referência de pacote [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) será necessária.</span><span class="sxs-lookup"><span data-stu-id="51cd4-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="51cd4-174">O `Microsoft.AspNetCore.Mvc` pacote foi movido para a estrutura compartilhada no ASP.NET Core 3,0 e, portanto, não é mais publicado.</span><span class="sxs-lookup"><span data-stu-id="51cd4-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="51cd4-175">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="51cd4-176">Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="51cd4-176">Tag Helpers</span></span>

<span data-ttu-id="51cd4-177">Um projeto que inclui [auxiliares de marca](xref:mvc/views/tag-helpers/intro) deve usar o `Microsoft.NET.Sdk` SDK.</span><span class="sxs-lookup"><span data-stu-id="51cd4-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="51cd4-178">Se estiver direcionando o .NET Core 3. x, adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="51cd4-179">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="51cd4-180">Se estiver direcionando .NET Standard (para dar suporte a versões anteriores a ASP.NET Core 3. x), adicione uma referência de pacote a [Microsoft Razor . AspNetCore. Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span><span class="sxs-lookup"><span data-stu-id="51cd4-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="51cd4-181">O `Microsoft.AspNetCore.Mvc.Razor` pacote foi movido para a estrutura compartilhada e, portanto, não é mais publicado.</span><span class="sxs-lookup"><span data-stu-id="51cd4-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="51cd4-182">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="51cd4-183">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="51cd4-183">View components</span></span>

<span data-ttu-id="51cd4-184">Um projeto que inclui [componentes de exibição](xref:mvc/views/view-components) deve usar o `Microsoft.NET.Sdk` SDK.</span><span class="sxs-lookup"><span data-stu-id="51cd4-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="51cd4-185">Se estiver direcionando o .NET Core 3. x, adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="51cd4-186">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="51cd4-187">Se estiver direcionando .NET Standard (para dar suporte a versões anteriores a ASP.NET Core 3. x), adicione uma referência de pacote a [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="51cd4-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="51cd4-188">O `Microsoft.AspNetCore.Mvc.ViewFeatures` pacote foi movido para a estrutura compartilhada e, portanto, não é mais publicado.</span><span class="sxs-lookup"><span data-stu-id="51cd4-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="51cd4-189">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="51cd4-190">Suporte a várias versões de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51cd4-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="51cd4-191">O direcionamento múltiplo é necessário para criar uma biblioteca que dê suporte a várias variantes de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="51cd4-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="51cd4-192">Considere um cenário no qual uma biblioteca de auxiliares de marcas deve dar suporte às seguintes ASP.NET Core variantes:</span><span class="sxs-lookup"><span data-stu-id="51cd4-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="51cd4-193">ASP.NET Core 2,1 direcionando .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="51cd4-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="51cd4-194">ASP.NET Core 2. x com direcionamento para .NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="51cd4-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="51cd4-195">ASP.NET Core 3. x com direcionamento para o .NET Core 3. x</span><span class="sxs-lookup"><span data-stu-id="51cd4-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="51cd4-196">O arquivo de projeto a seguir dá suporte a essas variantes por meio da `TargetFrameworks` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="51cd4-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="51cd4-197">Com o arquivo de projeto anterior:</span><span class="sxs-lookup"><span data-stu-id="51cd4-197">With the preceding project file:</span></span>

* <span data-ttu-id="51cd4-198">O `Markdig` pacote é adicionado para todos os consumidores.</span><span class="sxs-lookup"><span data-stu-id="51cd4-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="51cd4-199">Uma referência a [Microsoft. AspNetCore. Mvc. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span><span class="sxs-lookup"><span data-stu-id="51cd4-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span></span> <span data-ttu-id="51cd4-200">é adicionado para consumidores que visam .NET Framework 4.6.1 ou posterior ou o .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="51cd4-200">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="51cd4-201">A versão 2.1.0 do pacote funciona com o ASP.NET Core 2,2 devido à compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="51cd4-201">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="51cd4-202">A estrutura compartilhada é referenciada para os consumidores que visam o .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="51cd4-202">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="51cd4-203">O `Microsoft.AspNetCore.Mvc.Razor` pacote está incluído na estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-203">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="51cd4-204">Como alternativa, .NET Standard 2,0 poderia ser direcionado em vez de direcionar o .NET Core 2,1 e .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="51cd4-204">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="51cd4-205">Com o arquivo de projeto anterior, existem as seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="51cd4-205">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="51cd4-206">Como a biblioteca contém apenas auxiliares de marca, é mais simples direcionar para as plataformas específicas em que ASP.NET Core é executado: .NET Core e .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="51cd4-206">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="51cd4-207">Os auxiliares de marca não podem ser usados por outras estruturas de destino compatíveis com .NET Standard 2,0, como Unity, UWP e Xamarin.</span><span class="sxs-lookup"><span data-stu-id="51cd4-207">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="51cd4-208">Usar o .NET Standard 2.0 do .NET Framework tem alguns problemas que foram resolvidos no .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="51cd4-208">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="51cd4-209">Você pode melhorar a experiência dos consumidores usando o .NET Framework 4.6.1 até o 4.7.1 direcionando .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="51cd4-209">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="51cd4-210">Se sua biblioteca precisar chamar APIs específicas da plataforma, direcione as implementações específicas do .NET em vez de .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="51cd4-210">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="51cd4-211">Para obter mais informações, consulte [multi-Targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="51cd4-211">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="51cd4-212">Usar uma API que não foi alterada</span><span class="sxs-lookup"><span data-stu-id="51cd4-212">Use an API that hasn't changed</span></span>

<span data-ttu-id="51cd4-213">Imagine um cenário no qual você está atualizando uma biblioteca de middleware do .NET Core 2,2 para 3,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-213">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="51cd4-214">As APIs de middleware ASP.NET Core que estão sendo usadas na biblioteca não foram alteradas entre ASP.NET Core 2,2 e 3,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-214">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="51cd4-215">Para continuar a oferecer suporte à biblioteca de middleware no .NET Core 3,0, execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="51cd4-215">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="51cd4-216">Siga as [diretrizes da biblioteca padrão](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="51cd4-216">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="51cd4-217">Adicione uma referência de pacote para cada pacote NuGet de API se o assembly correspondente não existir na estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-217">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="51cd4-218">Usar uma API que foi alterada</span><span class="sxs-lookup"><span data-stu-id="51cd4-218">Use an API that changed</span></span>

<span data-ttu-id="51cd4-219">Imagine um cenário no qual você está atualizando uma biblioteca do .NET Core 2,2 para o .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-219">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="51cd4-220">Uma API de ASP.NET Core que está sendo usada na biblioteca tem uma [alteração significativa](/dotnet/core/compatibility/breaking-changes) no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-220">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="51cd4-221">Considere se a biblioteca pode ser reescrita para não usar a API quebrada em todas as versões.</span><span class="sxs-lookup"><span data-stu-id="51cd4-221">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="51cd4-222">Se você puder reescrever a biblioteca, faça-a e continue a direcionar uma estrutura de destino anterior (por exemplo, .NET Standard 2,0 ou .NET Framework 4.6.1) com referências de pacote.</span><span class="sxs-lookup"><span data-stu-id="51cd4-222">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="51cd4-223">Se você não puder reescrever a biblioteca, execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="51cd4-223">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="51cd4-224">Adicione um destino para o .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-224">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="51cd4-225">Adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-225">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="51cd4-226">Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar o código condicionalmente.</span><span class="sxs-lookup"><span data-stu-id="51cd4-226">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="51cd4-227">Por exemplo, leituras e gravações síncronas em fluxos de solicitação e resposta HTTP são desabilitadas por padrão a partir de ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-227">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="51cd4-228">O ASP.NET Core 2,2 dá suporte ao comportamento síncrono por padrão.</span><span class="sxs-lookup"><span data-stu-id="51cd4-228">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="51cd4-229">Considere uma biblioteca de middleware na qual leituras e gravações síncronas devem ser habilitadas onde e/s está ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="51cd4-229">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="51cd4-230">A biblioteca deve incluir o código para habilitar recursos síncronos na diretiva de pré-processador apropriada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-230">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="51cd4-231">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="51cd4-231">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="51cd4-232">Usar uma API introduzida em 3,0</span><span class="sxs-lookup"><span data-stu-id="51cd4-232">Use an API introduced in 3.0</span></span>

<span data-ttu-id="51cd4-233">Imagine que você deseja usar uma API de ASP.NET Core que foi introduzida no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-233">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="51cd4-234">Considere as perguntas a seguir:</span><span class="sxs-lookup"><span data-stu-id="51cd4-234">Consider the following questions:</span></span>

1. <span data-ttu-id="51cd4-235">A biblioteca requer a nova API?</span><span class="sxs-lookup"><span data-stu-id="51cd4-235">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="51cd4-236">A biblioteca pode implementar esse recurso de forma diferente?</span><span class="sxs-lookup"><span data-stu-id="51cd4-236">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="51cd4-237">Se a biblioteca exigir a API e não houver como implementá-la em nível inferior:</span><span class="sxs-lookup"><span data-stu-id="51cd4-237">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="51cd4-238">Somente o .NET Core 3. x de destino.</span><span class="sxs-lookup"><span data-stu-id="51cd4-238">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="51cd4-239">Adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-239">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="51cd4-240">Se a biblioteca puder implementar o recurso de uma maneira diferente:</span><span class="sxs-lookup"><span data-stu-id="51cd4-240">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="51cd4-241">Adicione o .NET Core 3. x como uma estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="51cd4-241">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="51cd4-242">Adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-242">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="51cd4-243">Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar o código condicionalmente.</span><span class="sxs-lookup"><span data-stu-id="51cd4-243">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="51cd4-244">Por exemplo, o auxiliar de marca a seguir usa a <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduzida no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="51cd4-244">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="51cd4-245">Os consumidores que visam o .NET Core 3,0 executam o caminho do código definido pelo `NETCOREAPP3_0` símbolo da estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="51cd4-245">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="51cd4-246">O tipo de parâmetro do construtor do auxiliar de marca é alterado para <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para .NET Core 2,1 e .NET Framework consumidores do 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="51cd4-246">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="51cd4-247">Essa alteração foi necessária porque ASP.NET Core 3,0 marcado `IHostingEnvironment` como obsoleto e recomendado `IWebHostEnvironment` como substituição.</span><span class="sxs-lookup"><span data-stu-id="51cd4-247">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="51cd4-248">O seguinte arquivo de projeto multiplataforma dá suporte a este cenário auxiliar de marca:</span><span class="sxs-lookup"><span data-stu-id="51cd4-248">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="51cd4-249">Usar uma API removida da estrutura compartilhada</span><span class="sxs-lookup"><span data-stu-id="51cd4-249">Use an API removed from the shared framework</span></span>

<span data-ttu-id="51cd4-250">Para usar um assembly ASP.NET Core que foi removido da estrutura compartilhada, adicione a referência de pacote apropriada.</span><span class="sxs-lookup"><span data-stu-id="51cd4-250">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="51cd4-251">Para obter uma lista de pacotes removidos da estrutura compartilhada no ASP.NET Core 3,0, consulte [remover referências de pacote obsoletos](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="51cd4-251">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="51cd4-252">Por exemplo, para adicionar o cliente de API Web:</span><span class="sxs-lookup"><span data-stu-id="51cd4-252">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="51cd4-253">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="51cd4-253">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="51cd4-254">Suporte à implementação do .NET</span><span class="sxs-lookup"><span data-stu-id="51cd4-254">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="51cd4-255">Políticas de suporte do .NET</span><span class="sxs-lookup"><span data-stu-id="51cd4-255">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
