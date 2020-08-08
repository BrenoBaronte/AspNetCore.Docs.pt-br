---
title: Solucionar problemas de Localização no ASP.NET Core
author: hishamco
description: Saiba como diagnosticar problemas com a localização em aplicativos do ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 801ae3a708d23308cbf909ceeace160e3c0db1bd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016460"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="f84f7-103">Solucionar problemas de Localização no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f84f7-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="f84f7-104">Por [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="f84f7-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="f84f7-105">Este artigo contém instruções sobre como diagnosticar problemas de localização em aplicativos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f84f7-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="f84f7-106">Problemas de configuração da localização</span><span class="sxs-lookup"><span data-stu-id="f84f7-106">Localization configuration issues</span></span>

<span data-ttu-id="f84f7-107">**Ordem do middleware de localização**</span><span class="sxs-lookup"><span data-stu-id="f84f7-107">**Localization middleware order**</span></span>  
<span data-ttu-id="f84f7-108">O aplicativo pode não localizar porque o middleware de localização não está ordenado conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="f84f7-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="f84f7-109">Para resolver esse problema, verifique se o middleware de localização foi registrado antes do middleware do MVC.</span><span class="sxs-lookup"><span data-stu-id="f84f7-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="f84f7-110">Caso contrário, o middleware de localização não será aplicado.</span><span class="sxs-lookup"><span data-stu-id="f84f7-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="f84f7-111">**Caminho dos recursos de localização não encontrado**</span><span class="sxs-lookup"><span data-stu-id="f84f7-111">**Localization resources path not found**</span></span>

<span data-ttu-id="f84f7-112">**As culturas compatíveis em RequestCultureProvider não correspondem ao registrado uma vez**</span><span class="sxs-lookup"><span data-stu-id="f84f7-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="f84f7-113">Problemas de nomenclatura do arquivo de recurso</span><span class="sxs-lookup"><span data-stu-id="f84f7-113">Resource file naming issues</span></span>

<span data-ttu-id="f84f7-114">O ASP.NET Core tem regras e diretrizes predefinidas para a nomenclatura de arquivos de recurso de localização, que são descritas em detalhes [aqui](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="f84f7-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="f84f7-115">Recursos ausentes</span><span class="sxs-lookup"><span data-stu-id="f84f7-115">Missing resources</span></span>

<span data-ttu-id="f84f7-116">As causas comuns para não localizar recursos incluem:</span><span class="sxs-lookup"><span data-stu-id="f84f7-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="f84f7-117">Nomes de recursos digitados incorretamente no arquivo `resx` ou na solicitação do localizador.</span><span class="sxs-lookup"><span data-stu-id="f84f7-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="f84f7-118">O recurso está ausente do `resx` para alguns idiomas, mas presente em outros.</span><span class="sxs-lookup"><span data-stu-id="f84f7-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="f84f7-119">Se ainda houver problemas, verifique as mensagens de log de localização (que estão no nível de log `Debug`) para saber mais detalhes sobre os recursos ausentes.</span><span class="sxs-lookup"><span data-stu-id="f84f7-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="f84f7-120">_**Dica:** Ao usar `CookieRequestCultureProvider` , verifique se aspas simples não são usadas com as culturas dentro do cookie valor de localização. Por exemplo, `c='en-UK'|uic='en-US'` é um cookie valor inválido, enquanto `c=en-UK|uic=en-US` é um válido._</span><span class="sxs-lookup"><span data-stu-id="f84f7-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="f84f7-121">Problemas de bibliotecas de recursos e classes</span><span class="sxs-lookup"><span data-stu-id="f84f7-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="f84f7-122">Por padrão, o ASP.NET Core oferece uma maneira de permitir que as bibliotecas de classes localizem seus arquivos de recurso por meio de [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="f84f7-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="f84f7-123">Problemas comuns com bibliotecas de classes incluem:</span><span class="sxs-lookup"><span data-stu-id="f84f7-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="f84f7-124">Ausência do `ResourceLocationAttribute` em uma biblioteca de classes evitará que `ResourceManagerStringLocalizerFactory` descubra os recursos.</span><span class="sxs-lookup"><span data-stu-id="f84f7-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="f84f7-125">Nomenclatura do arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="f84f7-125">Resource file naming.</span></span> <span data-ttu-id="f84f7-126">Para saber mais, confira a seção [Problemas de nomenclatura do arquivo de recurso](#resource-file-naming-issues).</span><span class="sxs-lookup"><span data-stu-id="f84f7-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="f84f7-127">Altere o namespace raiz da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="f84f7-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="f84f7-128">Para saber mais, confira a seção [Problemas do namespace raiz](#root-namespace-issues).</span><span class="sxs-lookup"><span data-stu-id="f84f7-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="f84f7-129">CustomRequestCultureProvider não funciona conforme o esperado</span><span class="sxs-lookup"><span data-stu-id="f84f7-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="f84f7-130">A classe `RequestLocalizationOptions` tem três provedores padrão:</span><span class="sxs-lookup"><span data-stu-id="f84f7-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="f84f7-131">O [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) permite personalizar como a cultura de localização é fornecida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f84f7-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="f84f7-132">O `CustomRequestCultureProvider` é usado quando os provedores padrão não atendem aos seus requisitos.</span><span class="sxs-lookup"><span data-stu-id="f84f7-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="f84f7-133">Um motivo comum para o provedor personalizado não funcionar corretamente é não ser o primeiro provedor na lista `RequestCultureProviders`.</span><span class="sxs-lookup"><span data-stu-id="f84f7-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="f84f7-134">Para resolver o problema:</span><span class="sxs-lookup"><span data-stu-id="f84f7-134">To resolve this issue:</span></span>

- <span data-ttu-id="f84f7-135">Insira o provedor personalizado na posição 0 da lista `RequestCultureProviders`, desta maneira:</span><span class="sxs-lookup"><span data-stu-id="f84f7-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="f84f7-136">Use o método de extensão `AddInitialRequestCultureProvider` para definir o provedor personalizado como provedor inicial.</span><span class="sxs-lookup"><span data-stu-id="f84f7-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="f84f7-137">Problemas do namespace raiz</span><span class="sxs-lookup"><span data-stu-id="f84f7-137">Root Namespace issues</span></span>

<span data-ttu-id="f84f7-138">Quando o namespace raiz de um assembly for diferente do nome do assembly, a localização não funcionará por padrão.</span><span class="sxs-lookup"><span data-stu-id="f84f7-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="f84f7-139">Para evitar esse problema, use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), que é descrito em detalhes [aqui](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span><span class="sxs-lookup"><span data-stu-id="f84f7-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="f84f7-140">Isso pode ocorrer quando o nome de um projeto não é um identificador .NET válido.</span><span class="sxs-lookup"><span data-stu-id="f84f7-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="f84f7-141">Por exemplo `my-project-name.csproj` , usará o namespace raiz `my_project_name` e o nome do assembly `my-project-name` que levam a esse erro.</span><span class="sxs-lookup"><span data-stu-id="f84f7-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="f84f7-142">Recursos e ação de build</span><span class="sxs-lookup"><span data-stu-id="f84f7-142">Resources & Build Action</span></span>

<span data-ttu-id="f84f7-143">Se você usa arquivos de recurso para localização, é importante que eles tenham uma ação de build correta.</span><span class="sxs-lookup"><span data-stu-id="f84f7-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="f84f7-144">Eles precisam ser um **Recurso inserido**; caso contrário, o `ResourceStringLocalizer` não conseguirá localizar os recursos.</span><span class="sxs-lookup"><span data-stu-id="f84f7-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
