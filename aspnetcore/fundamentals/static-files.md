---
title: Arquivos estáticos no ASP.NET Core
author: rick-anderson
description: Saiba como fornecer e proteger arquivos estáticos e configurar comportamentos do middleware de hospedagem de arquivos estáticos em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: b1f84a936ee1327498abce660cd64f8d7d0a2864
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202793"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="7c127-103">Arquivos estáticos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c127-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c127-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="7c127-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="7c127-105">Arquivos estáticos, como HTML, CSS, imagens e JavaScript, são ativos que um aplicativo ASP.NET Core atende diretamente aos clientes por padrão.</span><span class="sxs-lookup"><span data-stu-id="7c127-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="7c127-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c127-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="7c127-107">Fornecer arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="7c127-107">Serve static files</span></span>

<span data-ttu-id="7c127-108">Os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto.</span><span class="sxs-lookup"><span data-stu-id="7c127-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="7c127-109">O diretório padrão é `{content root}/wwwroot` , mas pode ser alterado com o <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> método.</span><span class="sxs-lookup"><span data-stu-id="7c127-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="7c127-110">Para obter mais informações, consulte [raiz de conteúdo](xref:fundamentals/index#content-root) e [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="7c127-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="7c127-111">O método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> define a raiz do conteúdo como o diretório atual:</span><span class="sxs-lookup"><span data-stu-id="7c127-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="7c127-112">O código anterior foi criado com o modelo de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="7c127-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="7c127-113">Os arquivos estáticos podem ser acessados por meio de um caminho relativo à [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="7c127-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="7c127-114">Por exemplo, os modelos de projeto de **aplicativo Web** contêm várias pastas dentro da `wwwroot` pasta:</span><span class="sxs-lookup"><span data-stu-id="7c127-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="7c127-115">Considere criar a pasta *wwwroot/images* e adicionar o arquivo *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="7c127-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="7c127-116">O formato do URI para acessar um arquivo na `images` pasta é `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="7c127-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="7c127-117">Por exemplo, `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="7c127-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="7c127-118">Fornecer arquivos na raiz da Web</span><span class="sxs-lookup"><span data-stu-id="7c127-118">Serve files in web root</span></span>

<span data-ttu-id="7c127-119">Os modelos de aplicativo Web padrão chamam o <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> método no `Startup.Configure` , que permite que os arquivos estáticos sejam atendidos:</span><span class="sxs-lookup"><span data-stu-id="7c127-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="7c127-120">A sobrecarga do `UseStaticFiles` método sem parâmetros marca os arquivos na [raiz da Web](xref:fundamentals/index#web-root) como servable.</span><span class="sxs-lookup"><span data-stu-id="7c127-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="7c127-121">A marcação a seguir referencia *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="7c127-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="7c127-122">No código anterior, o caractere til `~/` aponta para a [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="7c127-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="7c127-123">Fornecer arquivos fora do diretório base</span><span class="sxs-lookup"><span data-stu-id="7c127-123">Serve files outside of web root</span></span>

<span data-ttu-id="7c127-124">Considere uma hierarquia de diretório na qual os arquivos estáticos a serem servidos residem fora da [raiz da Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="7c127-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="7c127-125">Uma solicitação pode acessar o `red-rose.jpg` arquivo Configurando o middleware de arquivo estático da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="7c127-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="7c127-126">No código anterior, a hierarquia de diretórios *MyStaticFiles* é exposta publicamente por meio do segmento do URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="7c127-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="7c127-127">Uma solicitação para `https://<hostname>/StaticFiles/images/red-rose.jpg` servir o arquivo de *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="7c127-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="7c127-128">A marcação a seguir faz referência a *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="7c127-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="7c127-129">Definir cabeçalhos de resposta HTTP</span><span class="sxs-lookup"><span data-stu-id="7c127-129">Set HTTP response headers</span></span>

<span data-ttu-id="7c127-130">Um <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objeto pode ser usado para definir cabeçalhos de resposta http.</span><span class="sxs-lookup"><span data-stu-id="7c127-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="7c127-131">Além de configurar o serviço de arquivos estáticos na [raiz da Web](xref:fundamentals/index#web-root), o código a seguir define o `Cache-Control` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="7c127-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="7c127-132">Os arquivos estáticos são publicamente armazenáveis em cache por 600 segundos:</span><span class="sxs-lookup"><span data-stu-id="7c127-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Cabeçalho de resposta mostrando que o cabeçalho Cache-Control foi adicionado](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="7c127-134">Autorização de arquivo estático</span><span class="sxs-lookup"><span data-stu-id="7c127-134">Static file authorization</span></span>

<span data-ttu-id="7c127-135">O middleware de arquivos estáticos não fornece verificações de autorização.</span><span class="sxs-lookup"><span data-stu-id="7c127-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="7c127-136">Todos os arquivos servidos por ele, incluindo aqueles em `wwwroot` , estão publicamente acessíveis.</span><span class="sxs-lookup"><span data-stu-id="7c127-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="7c127-137">Para fornecer arquivos com base na autorização:</span><span class="sxs-lookup"><span data-stu-id="7c127-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="7c127-138">Armazene-os fora do `wwwroot` e de qualquer diretório acessível ao middleware de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="7c127-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="7c127-139">Sirva-os por meio de um método de ação ao qual a autorização é aplicada e retorna um <xref:Microsoft.AspNetCore.Mvc.FileResult> objeto:</span><span class="sxs-lookup"><span data-stu-id="7c127-139">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="7c127-140">Pesquisa no diretório</span><span class="sxs-lookup"><span data-stu-id="7c127-140">Directory browsing</span></span>

<span data-ttu-id="7c127-141">A pesquisa no diretório permite a listagem de diretório em diretórios especificados.</span><span class="sxs-lookup"><span data-stu-id="7c127-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="7c127-142">A pesquisa no diretório é desabilitada por padrão por motivos de segurança.</span><span class="sxs-lookup"><span data-stu-id="7c127-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="7c127-143">Para obter mais informações, consulte [considerações](#sc).</span><span class="sxs-lookup"><span data-stu-id="7c127-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="7c127-144">Habilitar pesquisa no diretório com:</span><span class="sxs-lookup"><span data-stu-id="7c127-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="7c127-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7c127-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="7c127-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> em `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7c127-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="7c127-147">O código anterior permite a navegação no diretório da pasta *wwwroot/images* usando a URL `https://<hostname>/MyImages` , com links para cada arquivo e pasta:</span><span class="sxs-lookup"><span data-stu-id="7c127-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![navegação no diretório](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="7c127-149">Fornecer documentos padrão</span><span class="sxs-lookup"><span data-stu-id="7c127-149">Serve default documents</span></span>

<span data-ttu-id="7c127-150">A definição de uma página padrão fornece aos visitantes um ponto de partida em um site.</span><span class="sxs-lookup"><span data-stu-id="7c127-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="7c127-151">Para servir uma página padrão de `wwwroot` sem um URI totalmente qualificado, chame o <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> método:</span><span class="sxs-lookup"><span data-stu-id="7c127-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="7c127-152">`UseDefaultFiles` deve ser chamado antes de `UseStaticFiles` para fornecer o arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="7c127-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="7c127-153">`UseDefaultFiles` é um regravador de URL que não atende ao arquivo.</span><span class="sxs-lookup"><span data-stu-id="7c127-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="7c127-154">Com `UseDefaultFiles` o, solicitações para uma pasta na `wwwroot` pesquisa por:</span><span class="sxs-lookup"><span data-stu-id="7c127-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="7c127-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="7c127-155">*default.htm*</span></span>
* <span data-ttu-id="7c127-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="7c127-156">*default.html*</span></span>
* <span data-ttu-id="7c127-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="7c127-157">*index.htm*</span></span>
* <span data-ttu-id="7c127-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="7c127-158">*index.html*</span></span>

<span data-ttu-id="7c127-159">O primeiro arquivo encontrado na lista é fornecido como se a solicitação fosse o URI totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="7c127-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="7c127-160">A URL do navegador continua refletindo o URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="7c127-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="7c127-161">O seguinte código altera o nome de arquivo padrão para *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="7c127-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="7c127-162">O código a seguir mostra `Startup.Configure` o código anterior:</span><span class="sxs-lookup"><span data-stu-id="7c127-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="7c127-163">UseFileServer para documentos padrão</span><span class="sxs-lookup"><span data-stu-id="7c127-163">UseFileServer for default documents</span></span>

<span data-ttu-id="7c127-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina a funcionalidade de `UseStaticFiles` , `UseDefaultFiles` e opcionalmente `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="7c127-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="7c127-165">Chame `app.UseFileServer` para habilitar o serviço de arquivos estáticos e o arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="7c127-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="7c127-166">A navegação no diretório não está habilitada.</span><span class="sxs-lookup"><span data-stu-id="7c127-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="7c127-167">O código a seguir `Startup.Configure` mostra `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="7c127-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="7c127-168">O código a seguir permite o serviço de arquivos estáticos, o arquivo padrão e a pesquisa no diretório:</span><span class="sxs-lookup"><span data-stu-id="7c127-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="7c127-169">O código a seguir mostra `Startup.Configure` o código anterior:</span><span class="sxs-lookup"><span data-stu-id="7c127-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="7c127-170">Considere a seguinte hierarquia de diretórios:</span><span class="sxs-lookup"><span data-stu-id="7c127-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="7c127-171">O código a seguir permite o serviço de arquivos estáticos, o arquivo padrão e a pesquisa no diretório `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="7c127-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="7c127-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> deve ser chamado quando o `EnableDirectoryBrowsing` valor da propriedade é `true` .</span><span class="sxs-lookup"><span data-stu-id="7c127-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="7c127-173">Com o uso da hierarquia de arquivos e do código anterior, as URLs são resolvidas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="7c127-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="7c127-174">URI</span><span class="sxs-lookup"><span data-stu-id="7c127-174">URI</span></span>            |      <span data-ttu-id="7c127-175">Resposta</span><span class="sxs-lookup"><span data-stu-id="7c127-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="7c127-176">*MyStaticFiles/imagens/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="7c127-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="7c127-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="7c127-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="7c127-178">Se nenhum arquivo nomeado padrão existir no diretório *MyStaticFiles* , `https://<hostname>/StaticFiles` o retornará a listagem de diretório com links clicáveis:</span><span class="sxs-lookup"><span data-stu-id="7c127-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Lista de arquivos estáticos](static-files/_static/db2.png)

<span data-ttu-id="7c127-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> Execute um redirecionamento do lado do cliente a partir do URI de destino sem uma trilha `/`  para o URI de destino com uma à direita `/` .</span><span class="sxs-lookup"><span data-stu-id="7c127-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="7c127-181">Por exemplo, de `https://<hostname>/StaticFiles` para `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="7c127-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="7c127-182">As URLs relativas no diretório *StaticFiles* são inválidas sem uma barra à direita ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="7c127-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="7c127-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="7c127-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="7c127-184">A <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contém uma `Mappings` propriedade que serve como um mapeamento de extensões de arquivo para tipos de conteúdo MIME.</span><span class="sxs-lookup"><span data-stu-id="7c127-184">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="7c127-185">No exemplo a seguir, várias extensões de arquivo são mapeadas para tipos MIME conhecidos.</span><span class="sxs-lookup"><span data-stu-id="7c127-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="7c127-186">A extensão *. rtf* é substituída e *. mp4* é removido:</span><span class="sxs-lookup"><span data-stu-id="7c127-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="7c127-187">O código a seguir mostra `Startup.Configure` o código anterior:</span><span class="sxs-lookup"><span data-stu-id="7c127-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="7c127-188">Consulte [Tipos de conteúdo MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="7c127-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="7c127-189">Tipos de conteúdo não padrão</span><span class="sxs-lookup"><span data-stu-id="7c127-189">Non-standard content types</span></span>

<span data-ttu-id="7c127-190">O middleware de arquivo estático compreende quase 400 tipos de conteúdo de arquivo conhecidos.</span><span class="sxs-lookup"><span data-stu-id="7c127-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="7c127-191">Se o usuário solicitar um arquivo com um tipo de arquivo desconhecido, o middleware de arquivo estático passará a solicitação para o próximo middleware no pipeline.</span><span class="sxs-lookup"><span data-stu-id="7c127-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="7c127-192">Se nenhum middleware manipular a solicitação, uma resposta *404 Não Encontrado* será retornada.</span><span class="sxs-lookup"><span data-stu-id="7c127-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="7c127-193">Se a navegação no diretório estiver habilitada, um link para o arquivo será exibido na lista do diretório.</span><span class="sxs-lookup"><span data-stu-id="7c127-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="7c127-194">O seguinte código habilita o fornecimento de tipos desconhecidos e renderiza o arquivo desconhecido como uma imagem:</span><span class="sxs-lookup"><span data-stu-id="7c127-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="7c127-195">O código a seguir mostra `Startup.Configure` o código anterior:</span><span class="sxs-lookup"><span data-stu-id="7c127-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="7c127-196">Com o código anterior, uma solicitação para um arquivo com um tipo de conteúdo desconhecido é retornada como uma imagem.</span><span class="sxs-lookup"><span data-stu-id="7c127-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="7c127-197">A habilitação <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="7c127-197">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="7c127-198">Ela está desabilitada por padrão, e seu uso não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="7c127-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="7c127-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fornece uma alternativa mais segura para o fornecimento de arquivos com extensões não padrão.</span><span class="sxs-lookup"><span data-stu-id="7c127-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="7c127-200">Fornecer arquivos de vários locais</span><span class="sxs-lookup"><span data-stu-id="7c127-200">Serve files from multiple locations</span></span>

<span data-ttu-id="7c127-201">`UseStaticFiles` e `UseFileServer` o padrão para o provedor de arquivos que aponta para `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="7c127-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="7c127-202">Instâncias adicionais do `UseStaticFiles` e do `UseFileServer` podem ser fornecidas com outros provedores de arquivos para fornecer arquivos de outros locais.</span><span class="sxs-lookup"><span data-stu-id="7c127-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="7c127-203">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="7c127-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="7c127-204">Considerações de segurança para arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="7c127-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="7c127-205">`UseDirectoryBrowser` e `UseStaticFiles` podem causar a perda de segredos.</span><span class="sxs-lookup"><span data-stu-id="7c127-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="7c127-206">A desabilitação da navegação no diretório em produção é altamente recomendada.</span><span class="sxs-lookup"><span data-stu-id="7c127-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="7c127-207">Examine com atenção os diretórios que são habilitados por meio de `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="7c127-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="7c127-208">Todo o diretório e seus subdiretórios se tornam publicamente acessíveis.</span><span class="sxs-lookup"><span data-stu-id="7c127-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="7c127-209">Armazene arquivos adequados para servir ao público em um diretório dedicado, como `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="7c127-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="7c127-210">Separe esses arquivos de exibições do MVC, Razor páginas, arquivos de configuração, etc.</span><span class="sxs-lookup"><span data-stu-id="7c127-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="7c127-211">As URLs para o conteúdo exposto com `UseDirectoryBrowser` e `UseStaticFiles` estão sujeitas à diferenciação de maiúsculas e minúsculas e a restrições de caracteres do sistema de arquivos subjacente.</span><span class="sxs-lookup"><span data-stu-id="7c127-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="7c127-212">Por exemplo, o Windows não diferencia maiúsculas de minúsculas, mas o macOS e o Linux não são.</span><span class="sxs-lookup"><span data-stu-id="7c127-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="7c127-213">Os aplicativos ASP.NET Core hospedados no IIS usam o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para encaminhar todas as solicitações ao aplicativo, inclusive as solicitações de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="7c127-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="7c127-214">O manipulador de arquivos estáticos do IIS não é usado e não tem a chance de lidar com solicitações.</span><span class="sxs-lookup"><span data-stu-id="7c127-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="7c127-215">Conclua as etapas seguinte no Gerenciador do IIS para remover o manipulador de arquivos estáticos no IIS no nível do servidor ou do site:</span><span class="sxs-lookup"><span data-stu-id="7c127-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="7c127-216">Navegue para o recurso **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="7c127-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="7c127-217">Selecione **StaticFileModule** na lista.</span><span class="sxs-lookup"><span data-stu-id="7c127-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="7c127-218">Clique em **Remover** na barra lateral **Ações**.</span><span class="sxs-lookup"><span data-stu-id="7c127-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="7c127-219">Se o manipulador de arquivo estático do IIS estiver habilitado **e** o Módulo do ASP.NET Core não estiver configurado corretamente, os arquivos estáticos serão atendidos.</span><span class="sxs-lookup"><span data-stu-id="7c127-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="7c127-220">Isso acontece, por exemplo, se o arquivo *web.config* não foi implantado.</span><span class="sxs-lookup"><span data-stu-id="7c127-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="7c127-221">Coloque os arquivos de código, incluindo *. cs* e *. cshtml*, fora da [raiz da Web](xref:fundamentals/index#web-root)do projeto de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c127-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="7c127-222">Portanto, uma separação lógica é criada entre o conteúdo do lado do cliente do aplicativo e o código baseado em servidor.</span><span class="sxs-lookup"><span data-stu-id="7c127-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="7c127-223">Isso impede a perda de código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="7c127-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c127-224">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7c127-224">Additional resources</span></span>

* [<span data-ttu-id="7c127-225">Middleware</span><span class="sxs-lookup"><span data-stu-id="7c127-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="7c127-226">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c127-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c127-227">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7c127-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7c127-228">Arquivos estáticos, como HTML, CSS, imagens e JavaScript, são ativos que um aplicativo ASP.NET Core fornece diretamente para os clientes.</span><span class="sxs-lookup"><span data-stu-id="7c127-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="7c127-229">Algumas etapas de configuração são necessárias para habilitar o fornecimento desses arquivos.</span><span class="sxs-lookup"><span data-stu-id="7c127-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="7c127-230">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c127-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="7c127-231">Fornecer arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="7c127-231">Serve static files</span></span>

<span data-ttu-id="7c127-232">Os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto.</span><span class="sxs-lookup"><span data-stu-id="7c127-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="7c127-233">O diretório padrão é *{Content root}/wwwroot*, mas pode ser alterado por meio do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> método.</span><span class="sxs-lookup"><span data-stu-id="7c127-233">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="7c127-234">Consulte [Raiz de conteúdo](xref:fundamentals/index#content-root) e [Diretório base](xref:fundamentals/index#web-root) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="7c127-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="7c127-235">O host Web do aplicativo deve ser informado do diretório raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="7c127-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="7c127-236">O método `WebHost.CreateDefaultBuilder` define a raiz do conteúdo como o diretório atual:</span><span class="sxs-lookup"><span data-stu-id="7c127-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="7c127-237">Os arquivos estáticos podem ser acessados por meio de um caminho relativo à [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="7c127-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="7c127-238">Por exemplo, o modelo de projeto de **aplicativo Web** contém várias pastas dentro da `wwwroot` pasta:</span><span class="sxs-lookup"><span data-stu-id="7c127-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="7c127-239">O formato do URI para acessar um arquivo na *images* subpasta imagens *é \<server_address> http:// \<image_file_name> /images/*.</span><span class="sxs-lookup"><span data-stu-id="7c127-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="7c127-240">Por exemplo, *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="7c127-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="7c127-241">Se estiver direcionando o .NET Framework, adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="7c127-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="7c127-242">Se você estiver direcionando para o .NET Core, o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluirá esse pacote.</span><span class="sxs-lookup"><span data-stu-id="7c127-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="7c127-243">Configure o [middleware](xref:fundamentals/middleware/index), que permite o serviço de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="7c127-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="7c127-244">Fornecer arquivos dentro do diretório base</span><span class="sxs-lookup"><span data-stu-id="7c127-244">Serve files inside of web root</span></span>

<span data-ttu-id="7c127-245">Invocar o <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> método em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7c127-245">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="7c127-246">A sobrecarga do `UseStaticFiles` método sem parâmetros marca os arquivos na [raiz da Web](xref:fundamentals/index#web-root) como servable.</span><span class="sxs-lookup"><span data-stu-id="7c127-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="7c127-247">A seguinte marcação referencia *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="7c127-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="7c127-248">No código anterior, o caractere til `~/` aponta para a [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="7c127-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="7c127-249">Fornecer arquivos fora do diretório base</span><span class="sxs-lookup"><span data-stu-id="7c127-249">Serve files outside of web root</span></span>

<span data-ttu-id="7c127-250">Considere uma hierarquia de diretório na qual os arquivos estáticos a serem servidos residem fora da [raiz da Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="7c127-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="7c127-251">Uma solicitação pode acessar o arquivo *banner1.svg* configurando o middleware de arquivos estáticos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="7c127-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="7c127-252">No código anterior, a hierarquia de diretórios *MyStaticFiles* é exposta publicamente por meio do segmento do URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="7c127-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="7c127-253">Uma solicitação para *http:// \<server_address> /StaticFiles/images/banner1.svg* serve o arquivo *banner1. svg* .</span><span class="sxs-lookup"><span data-stu-id="7c127-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="7c127-254">A seguinte marcação referencia *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="7c127-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="7c127-255">Definir cabeçalhos de resposta HTTP</span><span class="sxs-lookup"><span data-stu-id="7c127-255">Set HTTP response headers</span></span>

<span data-ttu-id="7c127-256">Um <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objeto pode ser usado para definir cabeçalhos de resposta http.</span><span class="sxs-lookup"><span data-stu-id="7c127-256">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="7c127-257">Além de configurar o serviço de arquivos estáticos na [raiz da Web](xref:fundamentals/index#web-root), o código a seguir define o `Cache-Control` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="7c127-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="7c127-258">O <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> método existe no pacote [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="7c127-258">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="7c127-259">Os arquivos se tornaram armazenáveis em cache publicamente por 10 minutos (600 segundos) no ambiente de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="7c127-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Cabeçalho de resposta mostrando que o cabeçalho Cache-Control foi adicionado](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="7c127-261">Autorização de arquivo estático</span><span class="sxs-lookup"><span data-stu-id="7c127-261">Static file authorization</span></span>

<span data-ttu-id="7c127-262">O middleware de arquivos estáticos não fornece verificações de autorização.</span><span class="sxs-lookup"><span data-stu-id="7c127-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="7c127-263">Todos os arquivos atendidos, incluindo aqueles em *wwwroot*, estão acessíveis publicamente.</span><span class="sxs-lookup"><span data-stu-id="7c127-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="7c127-264">Para fornecer arquivos com base na autorização:</span><span class="sxs-lookup"><span data-stu-id="7c127-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="7c127-265">Armazene-os fora do *wwwroot* e de qualquer diretório acessível ao middleware de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="7c127-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="7c127-266">Forneça-os por meio de um método de ação ao qual a autorização é aplicada.</span><span class="sxs-lookup"><span data-stu-id="7c127-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="7c127-267">Retornar um <xref:Microsoft.AspNetCore.Mvc.FileResult> objeto:</span><span class="sxs-lookup"><span data-stu-id="7c127-267">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="7c127-268">Habilitar navegação no diretório</span><span class="sxs-lookup"><span data-stu-id="7c127-268">Enable directory browsing</span></span>

<span data-ttu-id="7c127-269">A navegação no diretório permite que os usuários do aplicativo Web vejam uma listagem de diretório e arquivos em um diretório especificado.</span><span class="sxs-lookup"><span data-stu-id="7c127-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="7c127-270">A navegação no diretório está desabilitada por padrão por motivos de segurança (consulte [Considerações](#sc)).</span><span class="sxs-lookup"><span data-stu-id="7c127-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="7c127-271">Habilite a pesquisa no diretório invocando o <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> método em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7c127-271">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="7c127-272">Adicione os serviços necessários invocando o <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> método de `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7c127-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="7c127-273">O código anterior permite a navegação no diretório da pasta *wwwroot/images* usando a URL *http:// \<server_address> /myImages*, com links para cada arquivo e pasta:</span><span class="sxs-lookup"><span data-stu-id="7c127-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![navegação no diretório](static-files/_static/dir-browse.png)

<span data-ttu-id="7c127-275">Consulte [Considerações](#considerations) para saber mais sobre os riscos de segurança ao habilitar a navegação.</span><span class="sxs-lookup"><span data-stu-id="7c127-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="7c127-276">Observe as duas chamadas `UseStaticFiles` no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="7c127-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="7c127-277">A primeira chamada permite o fornecimento de arquivos estáticos na pasta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="7c127-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="7c127-278">A segunda chamada habilita a navegação no diretório da pasta *wwwroot/images* usando a URL *http:// \<server_address> /myImages*:</span><span class="sxs-lookup"><span data-stu-id="7c127-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="7c127-279">Fornecer um documento padrão</span><span class="sxs-lookup"><span data-stu-id="7c127-279">Serve a default document</span></span>

<span data-ttu-id="7c127-280">Definir uma home page padrão fornece ao visitantes um ponto de partida lógico de ao visitar o site.</span><span class="sxs-lookup"><span data-stu-id="7c127-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="7c127-281">Para servir uma página padrão sem que o usuário qualifique totalmente o URI, chame o <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> método de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7c127-281">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="7c127-282">`UseDefaultFiles` deve ser chamado antes de `UseStaticFiles` para fornecer o arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="7c127-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="7c127-283">`UseDefaultFiles` é um rewriter de URL que, na verdade, não fornece o arquivo.</span><span class="sxs-lookup"><span data-stu-id="7c127-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="7c127-284">Habilite o middleware de arquivos estáticos por meio de `UseStaticFiles` para fornecer o arquivo.</span><span class="sxs-lookup"><span data-stu-id="7c127-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="7c127-285">Com `UseDefaultFiles`, as solicitações para uma pasta pesquisam:</span><span class="sxs-lookup"><span data-stu-id="7c127-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="7c127-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="7c127-286">*default.htm*</span></span>
* <span data-ttu-id="7c127-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="7c127-287">*default.html*</span></span>
* <span data-ttu-id="7c127-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="7c127-288">*index.htm*</span></span>
* <span data-ttu-id="7c127-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="7c127-289">*index.html*</span></span>

<span data-ttu-id="7c127-290">O primeiro arquivo encontrado na lista é fornecido como se a solicitação fosse o URI totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="7c127-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="7c127-291">A URL do navegador continua refletindo o URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="7c127-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="7c127-292">O seguinte código altera o nome de arquivo padrão para *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="7c127-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="7c127-293">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="7c127-293">UseFileServer</span></span>

<span data-ttu-id="7c127-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina a funcionalidade de `UseStaticFiles` , `UseDefaultFiles` e opcionalmente `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="7c127-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="7c127-295">O código a seguir permite o fornecimento de arquivos estáticos e do arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="7c127-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="7c127-296">A navegação no diretório não está habilitada.</span><span class="sxs-lookup"><span data-stu-id="7c127-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="7c127-297">O seguinte código baseia-se na sobrecarga sem parâmetros, habilitando a navegação no diretório:</span><span class="sxs-lookup"><span data-stu-id="7c127-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="7c127-298">Considere a seguinte hierarquia de diretórios:</span><span class="sxs-lookup"><span data-stu-id="7c127-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="7c127-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="7c127-299">**wwwroot**</span></span>
  * <span data-ttu-id="7c127-300">**CSS**</span><span class="sxs-lookup"><span data-stu-id="7c127-300">**css**</span></span>
  * <span data-ttu-id="7c127-301">**images**</span><span class="sxs-lookup"><span data-stu-id="7c127-301">**images**</span></span>
  * <span data-ttu-id="7c127-302">**JS**</span><span class="sxs-lookup"><span data-stu-id="7c127-302">**js**</span></span>
* <span data-ttu-id="7c127-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="7c127-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="7c127-304">**images**</span><span class="sxs-lookup"><span data-stu-id="7c127-304">**images**</span></span>
    * <span data-ttu-id="7c127-305">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="7c127-305">*banner1.svg*</span></span>
  * <span data-ttu-id="7c127-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="7c127-306">*default.html*</span></span>

<span data-ttu-id="7c127-307">O seguinte código habilita arquivos estáticos, arquivos padrão e a navegação no diretório de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="7c127-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="7c127-308">`AddDirectoryBrowser` precisa ser chamado quando o valor da propriedade `EnableDirectoryBrowsing` é `true`:</span><span class="sxs-lookup"><span data-stu-id="7c127-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="7c127-309">Com o uso da hierarquia de arquivos e do código anterior, as URLs são resolvidas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="7c127-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="7c127-310">URI</span><span class="sxs-lookup"><span data-stu-id="7c127-310">URI</span></span>            |                             <span data-ttu-id="7c127-311">Resposta</span><span class="sxs-lookup"><span data-stu-id="7c127-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="7c127-312">*http:// \<server_address> /StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="7c127-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="7c127-313">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="7c127-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="7c127-314">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="7c127-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="7c127-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="7c127-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="7c127-316">Se nenhum arquivo nomeado padrão existir no diretório *MyStaticFiles* , *http:// \<server_address> /StaticFiles* retornará a listagem de diretório com links clicáveis:</span><span class="sxs-lookup"><span data-stu-id="7c127-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista de arquivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="7c127-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> executam um redirecionamento do lado do cliente de `http://{SERVER ADDRESS}/StaticFiles` (sem uma barra à direita) para `http://{SERVER ADDRESS}/StaticFiles/` (com uma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="7c127-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="7c127-319">As URLs relativas dentro do diretório *StaticFiles* são inválidas sem uma barra "\" à direita.</span><span class="sxs-lookup"><span data-stu-id="7c127-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="7c127-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="7c127-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="7c127-321">A <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contém uma `Mappings` propriedade que serve como um mapeamento de extensões de arquivo para tipos de conteúdo MIME.</span><span class="sxs-lookup"><span data-stu-id="7c127-321">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="7c127-322">Na amostra a seguir, várias extensões de arquivo são registradas para tipos MIME conhecidos.</span><span class="sxs-lookup"><span data-stu-id="7c127-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="7c127-323">A extensão *.rtf* é substituída, e *.mp4* é removida.</span><span class="sxs-lookup"><span data-stu-id="7c127-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="7c127-324">Consulte [Tipos de conteúdo MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="7c127-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="7c127-325">Para obter informações sobre como usar um personalizado <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou configurar outros <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> em Blazor aplicativos de servidor, consulte <xref:blazor/fundamentals/additional-scenarios#static-files> .</span><span class="sxs-lookup"><span data-stu-id="7c127-325">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="7c127-326">Tipos de conteúdo não padrão</span><span class="sxs-lookup"><span data-stu-id="7c127-326">Non-standard content types</span></span>

<span data-ttu-id="7c127-327">O middleware de arquivo estático compreende quase 400 tipos de conteúdo de arquivo conhecidos.</span><span class="sxs-lookup"><span data-stu-id="7c127-327">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="7c127-328">Se o usuário solicitar um arquivo com um tipo desconhecido, o middleware de arquivo estático passará a solicitação para o próximo middleware no pipeline.</span><span class="sxs-lookup"><span data-stu-id="7c127-328">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="7c127-329">Se nenhum middleware manipular a solicitação, uma resposta *404 Não Encontrado* será retornada.</span><span class="sxs-lookup"><span data-stu-id="7c127-329">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="7c127-330">Se a navegação no diretório estiver habilitada, um link para o arquivo será exibido na lista do diretório.</span><span class="sxs-lookup"><span data-stu-id="7c127-330">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="7c127-331">O seguinte código habilita o fornecimento de tipos desconhecidos e renderiza o arquivo desconhecido como uma imagem:</span><span class="sxs-lookup"><span data-stu-id="7c127-331">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="7c127-332">Com o código anterior, uma solicitação para um arquivo com um tipo de conteúdo desconhecido é retornada como uma imagem.</span><span class="sxs-lookup"><span data-stu-id="7c127-332">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="7c127-333">A habilitação <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="7c127-333">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="7c127-334">Ela está desabilitada por padrão, e seu uso não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="7c127-334">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="7c127-335">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fornece uma alternativa mais segura para o fornecimento de arquivos com extensões não padrão.</span><span class="sxs-lookup"><span data-stu-id="7c127-335">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="7c127-336">Fornecer arquivos de vários locais</span><span class="sxs-lookup"><span data-stu-id="7c127-336">Serve files from multiple locations</span></span>

<span data-ttu-id="7c127-337">`UseStaticFiles` e `UseFileServer` usa como padrão o provedor de arquivos que aponta em *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="7c127-337">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="7c127-338">Você pode fornecer instâncias adicionais do `UseStaticFiles` e `UseFileServer` com outros provedores de arquivos para fornecer arquivos de outros locais.</span><span class="sxs-lookup"><span data-stu-id="7c127-338">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="7c127-339">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="7c127-339">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="7c127-340">Considerações</span><span class="sxs-lookup"><span data-stu-id="7c127-340">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="7c127-341">`UseDirectoryBrowser` e `UseStaticFiles` podem causar a perda de segredos.</span><span class="sxs-lookup"><span data-stu-id="7c127-341">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="7c127-342">A desabilitação da navegação no diretório em produção é altamente recomendada.</span><span class="sxs-lookup"><span data-stu-id="7c127-342">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="7c127-343">Examine com atenção os diretórios que são habilitados por meio de `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="7c127-343">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="7c127-344">Todo o diretório e seus subdiretórios se tornam publicamente acessíveis.</span><span class="sxs-lookup"><span data-stu-id="7c127-344">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="7c127-345">Armazene arquivos adequados para servir ao público em um diretório dedicado, como \* \<content_root> /wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="7c127-345">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="7c127-346">Separe esses arquivos de exibições do MVC, Razor páginas (somente 2. x), arquivos de configuração, etc.</span><span class="sxs-lookup"><span data-stu-id="7c127-346">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="7c127-347">As URLs para o conteúdo exposto com `UseDirectoryBrowser` e `UseStaticFiles` estão sujeitas à diferenciação de maiúsculas e minúsculas e a restrições de caracteres do sistema de arquivos subjacente.</span><span class="sxs-lookup"><span data-stu-id="7c127-347">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="7c127-348">Por exemplo, o Windows diferencia maiúsculas de minúsculas, o macOS e o Linux não.</span><span class="sxs-lookup"><span data-stu-id="7c127-348">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="7c127-349">Os aplicativos ASP.NET Core hospedados no IIS usam o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para encaminhar todas as solicitações ao aplicativo, inclusive as solicitações de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="7c127-349">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="7c127-350">O manipulador de arquivos estáticos do IIS não é usado.</span><span class="sxs-lookup"><span data-stu-id="7c127-350">The IIS static file handler isn't used.</span></span> <span data-ttu-id="7c127-351">Ele não tem nenhuma possibilidade de manipular as solicitações antes que elas sejam manipuladas pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="7c127-351">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="7c127-352">Conclua as etapas seguinte no Gerenciador do IIS para remover o manipulador de arquivos estáticos no IIS no nível do servidor ou do site:</span><span class="sxs-lookup"><span data-stu-id="7c127-352">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="7c127-353">Navegue para o recurso **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="7c127-353">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="7c127-354">Selecione **StaticFileModule** na lista.</span><span class="sxs-lookup"><span data-stu-id="7c127-354">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="7c127-355">Clique em **Remover** na barra lateral **Ações**.</span><span class="sxs-lookup"><span data-stu-id="7c127-355">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="7c127-356">Se o manipulador de arquivo estático do IIS estiver habilitado **e** o Módulo do ASP.NET Core não estiver configurado corretamente, os arquivos estáticos serão atendidos.</span><span class="sxs-lookup"><span data-stu-id="7c127-356">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="7c127-357">Isso acontece, por exemplo, se o arquivo *web.config* não foi implantado.</span><span class="sxs-lookup"><span data-stu-id="7c127-357">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="7c127-358">Coloque os arquivos de código (incluindo *. cs* e *. cshtml*) fora da [raiz da Web](xref:fundamentals/index#web-root)do projeto de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c127-358">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="7c127-359">Portanto, uma separação lógica é criada entre o conteúdo do lado do cliente do aplicativo e o código baseado em servidor.</span><span class="sxs-lookup"><span data-stu-id="7c127-359">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="7c127-360">Isso impede a perda de código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="7c127-360">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c127-361">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7c127-361">Additional resources</span></span>

* [<span data-ttu-id="7c127-362">Middleware</span><span class="sxs-lookup"><span data-stu-id="7c127-362">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="7c127-363">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c127-363">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
