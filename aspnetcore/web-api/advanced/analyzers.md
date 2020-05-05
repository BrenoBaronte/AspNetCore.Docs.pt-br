---
title: Usar os analisadores da API Web
author: pranavkm
description: Saiba mais sobre o ASP.NET Core pacote de analisadores de API Web MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/analyzers
ms.openlocfilehash: 530ce2d2a7f67f549f6d188a0c571a5d58518377
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776240"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="86b73-103">Usar os analisadores da API Web</span><span class="sxs-lookup"><span data-stu-id="86b73-103">Use web API analyzers</span></span>

<span data-ttu-id="86b73-104">O ASP.NET Core 2,2 e posterior fornece um pacote de analisadores MVC destinado ao uso com projetos de API Web.</span><span class="sxs-lookup"><span data-stu-id="86b73-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="86b73-105">Os analisadores trabalham com controladores anotados com <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>o, ao criar [convenções de API Web](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="86b73-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="86b73-106">O pacote de analisadores notifica você sobre qualquer ação do controlador que:</span><span class="sxs-lookup"><span data-stu-id="86b73-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="86b73-107">Retorna um código de status não declarado.</span><span class="sxs-lookup"><span data-stu-id="86b73-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="86b73-108">Retorna um resultado de êxito não declarado.</span><span class="sxs-lookup"><span data-stu-id="86b73-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="86b73-109">Documenta um código de status que não é retornado.</span><span class="sxs-lookup"><span data-stu-id="86b73-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="86b73-110">Inclui uma verificação de validação de modelo explícita.</span><span class="sxs-lookup"><span data-stu-id="86b73-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="86b73-111">Referenciar o pacote do analisador</span><span class="sxs-lookup"><span data-stu-id="86b73-111">Reference the analyzer package</span></span>

<span data-ttu-id="86b73-112">No ASP.NET Core 3,0 ou posterior, os analisadores estão incluídos na SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="86b73-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="86b73-113">Para habilitar o analisador em seu projeto, inclua `IncludeOpenAPIAnalyzers` a propriedade no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="86b73-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="86b73-114">Instalação do pacote</span><span class="sxs-lookup"><span data-stu-id="86b73-114">Package installation</span></span>

<span data-ttu-id="86b73-115">Instale o pacote NuGet [Microsoft. AspNetCore. Mvc. API. Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="86b73-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="86b73-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86b73-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="86b73-117">Da janela **Console do Gerenciador de Pacotes**:</span><span class="sxs-lookup"><span data-stu-id="86b73-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="86b73-118">Vá para **Exibir** > **outro** > **console do Gerenciador de pacotes**do Windows.</span><span class="sxs-lookup"><span data-stu-id="86b73-118">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="86b73-119">Navegue até o diretório no qual o arquivo *ApiConventions.csproj* está localizado.</span><span class="sxs-lookup"><span data-stu-id="86b73-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="86b73-120">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="86b73-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="86b73-121">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="86b73-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="86b73-122">Clique com o botão direito do mouse na pasta *pacotes* em **painel de soluções** > **adicionar pacotes...**.</span><span class="sxs-lookup"><span data-stu-id="86b73-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="86b73-123">Defina a lista suspensa **fonte** da janela **adicionar pacotes** para "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="86b73-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="86b73-124">Insira "Microsoft.AspNetCore.Mvc.Api.Analyzers" na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="86b73-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="86b73-125">Selecione o pacote "Microsoft.AspNetCore.Mvc.Api.Analyzers" do painel de resultados e clique em **Adicionar Pacote**.</span><span class="sxs-lookup"><span data-stu-id="86b73-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="86b73-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86b73-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="86b73-127">Execute o comando a seguir do **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="86b73-127">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="86b73-128">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="86b73-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="86b73-129">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="86b73-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="86b73-130">Analisadores para convenções de API Web</span><span class="sxs-lookup"><span data-stu-id="86b73-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="86b73-131">Documentos de OpenAPI contêm códigos de status e tipos de resposta que uma ação pode retornar.</span><span class="sxs-lookup"><span data-stu-id="86b73-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="86b73-132">No ASP.NET Core MVC, atributos como <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> e <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> são usados para documentar uma ação.</span><span class="sxs-lookup"><span data-stu-id="86b73-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="86b73-133"><xref:tutorials/web-api-help-pages-using-swagger>apresenta mais detalhes sobre como documentar sua API Web.</span><span class="sxs-lookup"><span data-stu-id="86b73-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="86b73-134">Um dos analisadores no pacote inspeciona controladores anotados com <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> e identifica ações que não documentam totalmente as respostas.</span><span class="sxs-lookup"><span data-stu-id="86b73-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="86b73-135">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="86b73-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="86b73-136">A ação precedente documenta o tipo de retorno com êxito do HTTP 200, mas não documenta o código de status com falha do HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="86b73-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="86b73-137">O analisador relata a documentação ausente para o código de status HTTP 404 como um aviso.</span><span class="sxs-lookup"><span data-stu-id="86b73-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="86b73-138">É fornecida uma opção para consertar o problema.</span><span class="sxs-lookup"><span data-stu-id="86b73-138">An option to fix the problem is provided.</span></span>

![analisador relatando um aviso](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="86b73-140">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="86b73-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
