---
title: Novidades do ASP.NET Core 2.0
author: rick-anderson
description: Saiba mais sobre os novos recursos no ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.0
ms.openlocfilehash: f9fad5a63c76a3b21341a12fd40baafcdf2c9dac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059722"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="93687-103">Novidades do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="93687-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="93687-104">Este artigo destaca as alterações mais significativas no ASP.NET Core 2.0, com links para a documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="93687-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="no-locrazor-pages"></a><span data-ttu-id="93687-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="93687-105">Razor Pages</span></span>

<span data-ttu-id="93687-106">Razor As páginas são um novo recurso do ASP.NET Core MVC que torna os cenários voltados para a página de codificação mais fáceis e produtivos.</span><span class="sxs-lookup"><span data-stu-id="93687-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="93687-107">Para obter mais informações, consulte a introdução e o tutorial:</span><span class="sxs-lookup"><span data-stu-id="93687-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="93687-108">Introdução às Razor páginas</span><span class="sxs-lookup"><span data-stu-id="93687-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="93687-109">Introdução às Razor páginas</span><span class="sxs-lookup"><span data-stu-id="93687-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="93687-110">Metapacote do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93687-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="93687-111">Um novo metapacote do ASP.NET Core inclui todos os pacotes feitos e com suporte pelas equipes do ASP.NET Core e do Entity Framework Core, juntamente com as respectivas dependências internas e de terceiros.</span><span class="sxs-lookup"><span data-stu-id="93687-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="93687-112">Você não precisa mais escolher recursos individuais do ASP.NET Core por pacote.</span><span class="sxs-lookup"><span data-stu-id="93687-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="93687-113">Todos os recursos estão incluídos no pacote [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All).</span><span class="sxs-lookup"><span data-stu-id="93687-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="93687-114">Os modelos padrão usam este pacote.</span><span class="sxs-lookup"><span data-stu-id="93687-114">The default templates use this package.</span></span>

<span data-ttu-id="93687-115">Para obter mais informações, consulte [Metapacote do Microsoft.AspNetCore.All para ASP.NET Core 2.0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="93687-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="93687-116">Repositório de runtime</span><span class="sxs-lookup"><span data-stu-id="93687-116">Runtime Store</span></span>

<span data-ttu-id="93687-117">Aplicativos que usam o metapacote `Microsoft.AspNetCore.All` aproveitam automaticamente o novo repositório de runtime do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="93687-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="93687-118">O repositório contém todos os ativos de runtime necessários para executar aplicativos ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="93687-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="93687-119">Quando você usa o metapacote `Microsoft.AspNetCore.All`, nenhum ativo dos pacotes NuGet do ASP.NET Core referenciados são implantados com o aplicativo porque eles já estão no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="93687-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="93687-120">Os ativos no repositório de runtime também são pré-compilados para melhorar o tempo de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93687-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="93687-121">Para obter mais informações, consulte [Repositório de runtime](/dotnet/core/deploying/runtime-store)</span><span class="sxs-lookup"><span data-stu-id="93687-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="93687-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="93687-122">.NET Standard 2.0</span></span>

<span data-ttu-id="93687-123">Os pacotes do ASP.NET Core 2.0 são direcionados ao .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="93687-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="93687-124">Os pacotes podem ser referenciados por outras bibliotecas do .NET Standard 2.0 e podem ser executados em implementações em conformidade com o .NET Standard 2.0, incluindo o .NET Core 2.0 e o .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="93687-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="93687-125">O metapacote `Microsoft.AspNetCore.All` aborda apenas o .Net Core 2.0 porque ele foi projetado para ser utilizado com o repositório de runtime do .Net Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="93687-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="93687-126">Atualização da configuração</span><span class="sxs-lookup"><span data-stu-id="93687-126">Configuration update</span></span>

<span data-ttu-id="93687-127">Uma instância de `IConfiguration` é adicionada ao contêiner de serviços por padrão no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="93687-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="93687-128">`IConfiguration` no contêiner de serviços torna mais fácil para aplicativos recuperarem valores de configuração do contêiner.</span><span class="sxs-lookup"><span data-stu-id="93687-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="93687-129">Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="93687-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="93687-130">Atualização de registro em log</span><span class="sxs-lookup"><span data-stu-id="93687-130">Logging update</span></span>

<span data-ttu-id="93687-131">No ASP.NET Core 2.0, o log será incorporado no sistema de DI (injeção de dependência) por padrão.</span><span class="sxs-lookup"><span data-stu-id="93687-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="93687-132">Você adiciona provedores e configura a filtragem no arquivo *Program.cs* em vez de usar o arquivo *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="93687-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="93687-133">E o `ILoggerFactory` padrão dá suporte à filtragem de forma que lhe permite usar uma abordagem flexível para filtragem entre provedores e filtragem específica do provedor.</span><span class="sxs-lookup"><span data-stu-id="93687-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="93687-134">Para obter mais informações, consulte [introdução ao registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="93687-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="93687-135">Atualização de autenticação</span><span class="sxs-lookup"><span data-stu-id="93687-135">Authentication update</span></span>

<span data-ttu-id="93687-136">Um novo modelo de autenticação torna mais fácil configurar a autenticação para um aplicativo usando a DI.</span><span class="sxs-lookup"><span data-stu-id="93687-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="93687-137">Novos modelos estão disponíveis para configurar a autenticação para aplicativos Web e APIs Web usando o [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="93687-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="93687-138">Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="93687-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="no-locidentity-update"></a><span data-ttu-id="93687-139">atualização de Identity</span><span class="sxs-lookup"><span data-stu-id="93687-139">Identity update</span></span>

<span data-ttu-id="93687-140">Tornamos mais fácil criar APIs Web seguras usando o Identity no ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="93687-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="93687-141">Você pode adquirir tokens de acesso para acessar suas APIs Web usando a [MSAL (Biblioteca de Autenticação da Microsoft)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span><span class="sxs-lookup"><span data-stu-id="93687-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="93687-142">Para obter mais informações sobre alterações de autenticação no 2.0, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="93687-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="93687-143">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93687-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="93687-144">Habilitar a geração de código QR para aplicativos de autenticador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93687-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="93687-145">Migrar autenticação e Identity para ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="93687-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="93687-146">Modelos do SPA</span><span class="sxs-lookup"><span data-stu-id="93687-146">SPA templates</span></span>

<span data-ttu-id="93687-147">Modelos de projeto de SPA (aplicativo de página único) para Angular, Aurelia, Knockout.js, React.js e React.js com Redux estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="93687-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="93687-148">O modelo Angular foi atualizado para Angular 4.</span><span class="sxs-lookup"><span data-stu-id="93687-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="93687-149">Os modelos Angular e React estão disponíveis por padrão. Para saber como obter os outros modelos, confira [Criar um novo projeto de SPA](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="93687-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="93687-150">Para obter informações sobre como criar um SPA no ASP.NET Core, confira <xref:client-side/spa-services>.</span><span class="sxs-lookup"><span data-stu-id="93687-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="93687-151">Melhorias do Kestrel</span><span class="sxs-lookup"><span data-stu-id="93687-151">Kestrel improvements</span></span>

<span data-ttu-id="93687-152">O servidor Web Kestrel tem novos recursos que o tornam mais adequado como um servidor voltado para a Internet.</span><span class="sxs-lookup"><span data-stu-id="93687-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="93687-153">Uma série de opções de configuração de restrição de servidor serão adicionadas na nova propriedade `Limits` da classe `KestrelServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="93687-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="93687-154">Adicione limites para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="93687-154">Add limits for the following:</span></span>

* <span data-ttu-id="93687-155">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="93687-155">Maximum client connections</span></span>
* <span data-ttu-id="93687-156">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="93687-156">Maximum request body size</span></span>
* <span data-ttu-id="93687-157">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="93687-157">Minimum request body data rate</span></span>

<span data-ttu-id="93687-158">Para obter mais informações, consulte [Implementação do servidor web Kestrel no ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="93687-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="93687-159">WebListener renomeado para HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="93687-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="93687-160">Os pacotes `Microsoft.AspNetCore.Server.WebListener` e `Microsoft.Net.Http.Server` foram mesclados em um novo pacote `Microsoft.AspNetCore.Server.HttpSys`.</span><span class="sxs-lookup"><span data-stu-id="93687-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="93687-161">Os namespaces foram atualizados para corresponderem.</span><span class="sxs-lookup"><span data-stu-id="93687-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="93687-162">Para obter mais informações, consulte [Implementação do servidor Web HTTP.sys no ASP.NET Core](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="93687-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="93687-163">Suporte aprimorado a cabeçalho HTTP</span><span class="sxs-lookup"><span data-stu-id="93687-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="93687-164">Ao usar o MVC para transmitir um `FileStreamResult` ou um `FileContentResult`, agora você tem a opção de definir uma `ETag` ou uma data `LastModified` no conteúdo que você transmitir.</span><span class="sxs-lookup"><span data-stu-id="93687-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="93687-165">Você pode definir esses valores no conteúdo retornado com código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="93687-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="93687-166">O arquivo retornado para os visitantes tem os cabeçalhos HTTP apropriados para os `ETag` `LastModified` valores e.</span><span class="sxs-lookup"><span data-stu-id="93687-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="93687-167">Se um visitante do aplicativo solicitar o conteúdo com um cabeçalho de solicitação de intervalo, o ASP.NET Core reconhecerá a solicitação e lidará com o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="93687-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="93687-168">Se parte do conteúdo solicitado puder ser entregue, o ASP.NET Core ignorará a parte em questão e retornará apenas o conjunto de bytes solicitado.</span><span class="sxs-lookup"><span data-stu-id="93687-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="93687-169">Você não precisa gravar nenhum manipulador especial em seus métodos para adaptar ou manipular esse recurso; ele é manipulado automaticamente para você.</span><span class="sxs-lookup"><span data-stu-id="93687-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="93687-170">Inicialização de hospedagem e o Application Insights</span><span class="sxs-lookup"><span data-stu-id="93687-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="93687-171">Ambientes de hospedagem podem injetar dependências de pacote extras e executar código durante a inicialização do aplicativo, sem que o aplicativo precise tomar uma dependência explicitamente ou chamar algum método.</span><span class="sxs-lookup"><span data-stu-id="93687-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="93687-172">Esse recurso pode ser usado para habilitar determinados ambientes para recursos de "esclarecimento" exclusivos para esse ambiente sem que o aplicativo precise saber antecipadamente.</span><span class="sxs-lookup"><span data-stu-id="93687-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="93687-173">No ASP.NET Core 2.0, esse recurso é usado para habilitar o diagnóstico do Application Insights automaticamente durante a depuração no Visual Studio e (depois de optar por isto) quando em execução nos Serviços de Aplicativos do Azure.</span><span class="sxs-lookup"><span data-stu-id="93687-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="93687-174">Como resultado, os modelos de projeto não adicionam mais código e pacotes do Application Insights por padrão.</span><span class="sxs-lookup"><span data-stu-id="93687-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="93687-175">Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="93687-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="93687-176">Uso automático de tokens antifalsificação</span><span class="sxs-lookup"><span data-stu-id="93687-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="93687-177">O ASP.NET Core sempre ajudou a fazer a codificação HTML do conteúdo por padrão, mas com a nova versão é necessário um passo adicional para ajudar a impedir ataques de XSRF (falsificação de solicitação entre sites).</span><span class="sxs-lookup"><span data-stu-id="93687-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="93687-178">O ASP.NET Core agora emitirá tokens antifalsificação por padrão e os validará em ações de POST de formulário e em páginas sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="93687-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="93687-179">Para obter mais informações, consulte <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="93687-179">For more information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="93687-180">Pré-compilação automática</span><span class="sxs-lookup"><span data-stu-id="93687-180">Automatic precompilation</span></span>

<span data-ttu-id="93687-181">Razor a exibição de pré-compilação é habilitada durante a publicação por padrão, reduzindo o tamanho da saída de publicação e o tempo de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93687-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="93687-182">Para obter mais informações, consulte [ Razor Exibir compilação e pré-compilação no ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="93687-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="no-locrazor-support-for-c-71"></a><span data-ttu-id="93687-183">Razor suporte para C# 7,1</span><span class="sxs-lookup"><span data-stu-id="93687-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="93687-184">O Razor mecanismo de exibição foi atualizado para funcionar com o novo compilador Roslyn.</span><span class="sxs-lookup"><span data-stu-id="93687-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="93687-185">Isso inclui suporte para recursos do C# 7.1 como expressões padrão, nomes de tupla inferidos e correspondência de padrões com genéricos.</span><span class="sxs-lookup"><span data-stu-id="93687-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="93687-186">Para usar o C# 7.1 em seu projeto, adicione a seguinte propriedade no arquivo de projeto e, em seguida, recarregue a solução:</span><span class="sxs-lookup"><span data-stu-id="93687-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="93687-187">Para obter informações sobre o status dos recursos do C# 7.1, consulte [o repositório GitHub do Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="93687-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="93687-188">Outras atualizações de documentação para 2.0</span><span class="sxs-lookup"><span data-stu-id="93687-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="93687-189">Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93687-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="93687-190">Gerenciamento de chaves</span><span class="sxs-lookup"><span data-stu-id="93687-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="93687-191">Configurar a autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="93687-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="93687-192">Configurar a autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="93687-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="93687-193">Configurar a autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="93687-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="93687-194">Configurar a autenticação da conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="93687-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="93687-195">Guia de migração</span><span class="sxs-lookup"><span data-stu-id="93687-195">Migration guidance</span></span>

<span data-ttu-id="93687-196">Para obter diretrizes sobre como migrar aplicativos ASP.NET Core 1.x para o ASP.NET Core 2.0, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="93687-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="93687-197">Migrar do ASP.NET Core 1.x para o ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="93687-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="93687-198">Migrar autenticação e Identity para ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="93687-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="93687-199">Informações adicionais</span><span class="sxs-lookup"><span data-stu-id="93687-199">Additional Information</span></span>

<span data-ttu-id="93687-200">Para obter uma lista de alterações, consulte as [Notas de versão do ASP.NET Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="93687-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="93687-201">Para se conectar ao progresso e aos planos da equipe de desenvolvimento do ASP.NET Core, fique ligado no [ASP.NET Community Standup](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="93687-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
