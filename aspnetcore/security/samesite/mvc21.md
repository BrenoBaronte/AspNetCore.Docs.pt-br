---
title: Exemplo ASP.NET Core 2,1 MVC SameSite cookie
author: rick-anderson
description: Exemplo ASP.NET Core 2,1 MVC SameSite cookie
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: 0a719ae48199f7854ded534446045eb304d4d9f0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632350"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="20806-103">Exemplo ASP.NET Core 2,1 MVC SameSite cookie</span><span class="sxs-lookup"><span data-stu-id="20806-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="20806-104">ASP.NET Core 2,1 tem suporte interno para o atributo [SameSite](https://www.owasp.org/index.php/SameSite) , mas foi gravado no padrão original.</span><span class="sxs-lookup"><span data-stu-id="20806-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="20806-105">O [comportamento de patches](https://github.com/dotnet/aspnetcore/issues/8212) alterou o significado de `SameSite.None` para emitir o atributo sameSite com um valor de `None` , em vez de não emitir o valor de forma alguma.</span><span class="sxs-lookup"><span data-stu-id="20806-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="20806-106">Se você quiser não emitir o valor, poderá definir a `SameSite` propriedade em um cookie como-1.</span><span class="sxs-lookup"><span data-stu-id="20806-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="20806-107">Gravando o atributo SameSite</span><span class="sxs-lookup"><span data-stu-id="20806-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="20806-108">Veja a seguir um exemplo de como escrever um atributo SameSite em um cookie :</span><span class="sxs-lookup"><span data-stu-id="20806-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="20806-109">Definindo Cookie autenticação e estado da sessão cookie s</span><span class="sxs-lookup"><span data-stu-id="20806-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="20806-110">Cookie autenticação, estado de sessão e [vários outros componentes](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) definem suas opções de sameSite por meio Cookie de opções, por exemplo</span><span class="sxs-lookup"><span data-stu-id="20806-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="20806-111">No código anterior, o cookie estado de sessão e de autenticação definem seu atributo sameSite como `None` , emitindo o atributo com um `None` valor e também definimos o atributo Secure como true.</span><span class="sxs-lookup"><span data-stu-id="20806-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="20806-112">Execute o exemplo</span><span class="sxs-lookup"><span data-stu-id="20806-112">Run the sample</span></span>

<span data-ttu-id="20806-113">Se você executar o [projeto de exemplo](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), carregue o depurador do navegador na página inicial e use-o para exibir a cookie coleção para o site.</span><span class="sxs-lookup"><span data-stu-id="20806-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="20806-114">Para fazer isso no Edge e no Chrome `F12` , selecione a `Application` guia e clique na URL do site na `Cookies` opção na `Storage` seção.</span><span class="sxs-lookup"><span data-stu-id="20806-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Depurador de navegador::: no-Loc (cookie)::: list](BrowserDebugger.png)

<span data-ttu-id="20806-116">Você pode ver na imagem acima que o cookie criado pelo exemplo ao clicar no botão "Create SameSite Cookie " tem um valor de atributo SameSite de `Lax` , correspondendo ao valor definido no código de [exemplo](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="20806-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="20806-117">Interceptando cookie s</span><span class="sxs-lookup"><span data-stu-id="20806-117">Intercepting cookies</span></span>

<span data-ttu-id="20806-118">Para interceptar cookie s, para ajustar o valor None de acordo com seu suporte no agente de navegador do usuário, você deve usar o `CookiePolicy` middleware.</span><span class="sxs-lookup"><span data-stu-id="20806-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="20806-119">Isso deve ser colocado no pipeline de solicitação HTTP **antes** de quaisquer componentes que gravam cookie s e configurados no `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="20806-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="20806-120">Para inseri-lo no pipeline, use `app.UseCookiePolicy()` o `Configure(IApplicationBuilder, IHostingEnvironment)` método em [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="20806-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="20806-121">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20806-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="20806-122">Em seguida, em `ConfigureServices(IServiceCollection services)` Configurar a cookie política para chamar uma classe auxiliar quando cookie s forem acrescentadas ou excluídas.</span><span class="sxs-lookup"><span data-stu-id="20806-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="20806-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20806-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="20806-124">A função auxiliar `CheckSameSite(HttpContext, CookieOptions)` :</span><span class="sxs-lookup"><span data-stu-id="20806-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="20806-125">É chamado quando cookie s são acrescentadas à solicitação ou excluídas da solicitação.</span><span class="sxs-lookup"><span data-stu-id="20806-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="20806-126">Verifica se a `SameSite` propriedade está definida como `None` .</span><span class="sxs-lookup"><span data-stu-id="20806-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="20806-127">Se `SameSite` é definido como `None` e o agente do usuário atual é conhecido por não oferecer suporte ao valor de atributo None.</span><span class="sxs-lookup"><span data-stu-id="20806-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="20806-128">A verificação é feita usando a classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="20806-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="20806-129">Define `SameSite` como não emitir o valor definindo a propriedade como `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="20806-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="20806-130">Direcionamento .NET Framework</span><span class="sxs-lookup"><span data-stu-id="20806-130">Targeting .NET Framework</span></span>

<span data-ttu-id="20806-131">ASP.NET Core e System. Web (ASP.NET clássico) têm implementações independentes de SameSite.</span><span class="sxs-lookup"><span data-stu-id="20806-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="20806-132">Os patches do SameSite KB para .NET Framework não são necessários se usar ASP.NET Core nem o requisito de versão de estrutura mínima do System. Web SameSite (.NET 4.7.2) se aplicam ao ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20806-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="20806-133">ASP.NET Core no .NET requer a atualização das dependências do pacote NuGet para obter as correções apropriadas.</span><span class="sxs-lookup"><span data-stu-id="20806-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="20806-134">Para obter as alterações de ASP.NET Core para .NET Framework Verifique se você tem uma referência direta para os pacotes e versões de patch (2.1.14 ou versões posteriores 2,1).</span><span class="sxs-lookup"><span data-stu-id="20806-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="20806-135">Mais informações</span><span class="sxs-lookup"><span data-stu-id="20806-135">More Information</span></span>
 
<span data-ttu-id="20806-136">[Atualizações](https://www.chromium.org/updates/same-site) 
 do Chrome Documentação do ASP.NET Core [SameSite](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) 
 [Aviso de alteração do ASP.NET Core 2,1 SameSite](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="20806-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>