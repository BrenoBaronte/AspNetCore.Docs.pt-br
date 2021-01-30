---
title: Configurar pontos de extremidade para o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba como configurar pontos de extremidade com o Kestrel, o servidor Web de plataforma cruzada para ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: f9d82409f4b31a5564c7cdfa48beb303d784e213
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057142"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="481d1-103">Configurar pontos de extremidade para o servidor Web ASP.NET Core Kestrel</span><span class="sxs-lookup"><span data-stu-id="481d1-103">Configure endpoints for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="481d1-104">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="481d1-104">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="481d1-105">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="481d1-105">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="481d1-106">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="481d1-106">Specify URLs using the:</span></span>

* <span data-ttu-id="481d1-107">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="481d1-107">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="481d1-108">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="481d1-108">`--urls` command-line argument.</span></span>
* <span data-ttu-id="481d1-109">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="481d1-109">`urls` host configuration key.</span></span>
* <span data-ttu-id="481d1-110">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="481d1-110">`UseUrls` extension method.</span></span>

<span data-ttu-id="481d1-111">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="481d1-111">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="481d1-112">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="481d1-112">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="481d1-113">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="481d1-113">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="481d1-114">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="481d1-114">A development certificate is created:</span></span>

* <span data-ttu-id="481d1-115">Quando o [SDK do .net](/dotnet/core/sdk) é instalado.</span><span class="sxs-lookup"><span data-stu-id="481d1-115">When the [.NET SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="481d1-116">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="481d1-116">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="481d1-117">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="481d1-117">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="481d1-118">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="481d1-118">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="481d1-119">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="481d1-119">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="481d1-120">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="481d1-120">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="481d1-121">`KestrelServerOptions` configuração</span><span class="sxs-lookup"><span data-stu-id="481d1-121">`KestrelServerOptions` configuration:</span></span>

## <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="481d1-122">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="481d1-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="481d1-123">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="481d1-123">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="481d1-124">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="481d1-124">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="481d1-125">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="481d1-125">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configureiconfiguration"></a><span data-ttu-id="481d1-126">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="481d1-126">Configure(IConfiguration)</span></span>

<span data-ttu-id="481d1-127">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="481d1-127">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="481d1-128">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="481d1-128">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="481d1-129">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="481d1-129">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="481d1-130">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="481d1-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="481d1-131">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="481d1-131">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="481d1-132">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="481d1-132">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="481d1-133">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="481d1-133">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> won't have the defaults applied.</span></span>

## <a name="listenoptionsusehttps"></a><span data-ttu-id="481d1-134">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="481d1-134">ListenOptions.UseHttps</span></span>

<span data-ttu-id="481d1-135">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="481d1-135">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="481d1-136">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="481d1-136">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="481d1-137">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="481d1-137">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="481d1-138">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="481d1-138">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="481d1-139">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="481d1-139">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="481d1-140">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="481d1-140">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="481d1-141">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="481d1-141">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="481d1-142">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="481d1-142">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="481d1-143">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="481d1-143">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="481d1-144">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="481d1-144">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="481d1-145">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="481d1-145">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="481d1-146">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="481d1-146">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="481d1-147">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="481d1-147">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="481d1-148">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="481d1-148">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="481d1-149">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="481d1-149">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="481d1-150">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="481d1-150">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="481d1-151">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="481d1-151">Supported configurations described next:</span></span>

* <span data-ttu-id="481d1-152">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="481d1-152">No configuration</span></span>
* <span data-ttu-id="481d1-153">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="481d1-153">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="481d1-154">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="481d1-154">Change the defaults in code</span></span>

### <a name="no-configuration"></a><span data-ttu-id="481d1-155">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="481d1-155">No configuration</span></span>

<span data-ttu-id="481d1-156">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="481d1-156">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a><span data-ttu-id="481d1-157">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="481d1-157">Replace the default certificate from configuration</span></span>

<span data-ttu-id="481d1-158">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="481d1-158">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="481d1-159">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="481d1-159">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="481d1-160">No exemplo a seguir *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="481d1-160">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="481d1-161">Defina `AllowInvalid` como `true` para permitir o uso de certificados inválidos (por exemplo, certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="481d1-161">Set `AllowInvalid` to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="481d1-162">Qualquer ponto de extremidade HTTPS que não especifique um certificado ( `HttpsDefaultCert` no exemplo a seguir) volta para o certificado definido em `Certificates:Default` ou o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="481d1-162">Any HTTPS endpoint that doesn't specify a certificate (`HttpsDefaultCert` in the example that follows) falls back to the cert defined under `Certificates:Default` or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="481d1-163">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="481d1-163">Schema notes:</span></span>

* <span data-ttu-id="481d1-164">Os nomes dos pontos de extremidade não diferenciam [maiúsculas de minúsculas](xref:fundamentals/configuration/index#configuration-keys-and-values).</span><span class="sxs-lookup"><span data-stu-id="481d1-164">Endpoints names are [case-insensitive](xref:fundamentals/configuration/index#configuration-keys-and-values).</span></span> <span data-ttu-id="481d1-165">Por exemplo: `HTTPS` and `Https` são equivalentes.</span><span class="sxs-lookup"><span data-stu-id="481d1-165">For example, `HTTPS` and `Https` are equivalent.</span></span>
* <span data-ttu-id="481d1-166">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="481d1-166">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="481d1-167">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="481d1-167">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="481d1-168">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="481d1-168">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="481d1-169">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="481d1-169">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="481d1-170">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="481d1-170">The `Certificate` section is optional.</span></span> <span data-ttu-id="481d1-171">Se a `Certificate` seção não for especificada, os padrões definidos em `Certificates:Default` serão usados.</span><span class="sxs-lookup"><span data-stu-id="481d1-171">If the `Certificate` section isn't specified, the defaults defined in `Certificates:Default` are used.</span></span> <span data-ttu-id="481d1-172">Se nenhum padrão estiver disponível, o certificado de desenvolvimento será usado.</span><span class="sxs-lookup"><span data-stu-id="481d1-172">If no defaults are available, the development certificate is used.</span></span> <span data-ttu-id="481d1-173">Se não houver padrões e o certificado de desenvolvimento não estiver presente, o servidor gerará uma exceção e não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="481d1-173">If there are no defaults and the development certificate isn't present, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="481d1-174">A `Certificate` seção dá suporte a várias [fontes de certificado](#certificate-sources).</span><span class="sxs-lookup"><span data-stu-id="481d1-174">The `Certificate` section supports multiple [certificate sources](#certificate-sources).</span></span>
* <span data-ttu-id="481d1-175">Qualquer número de pontos de extremidade pode ser definido na [configuração](xref:fundamentals/configuration/index) contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="481d1-175">Any number of endpoints may be defined in [Configuration](xref:fundamentals/configuration/index) as long as they don't cause port conflicts.</span></span>

#### <a name="certificate-sources"></a><span data-ttu-id="481d1-176">Fontes de certificado</span><span class="sxs-lookup"><span data-stu-id="481d1-176">Certificate sources</span></span>

<span data-ttu-id="481d1-177">Os nós de certificado podem ser configurados para carregar certificados de várias fontes:</span><span class="sxs-lookup"><span data-stu-id="481d1-177">Certificate nodes can be configured to load certificates from a number of sources:</span></span>

* <span data-ttu-id="481d1-178">`Path` e `Password` carregar arquivos *. pfx* .</span><span class="sxs-lookup"><span data-stu-id="481d1-178">`Path` and `Password` to load *.pfx* files.</span></span>
* <span data-ttu-id="481d1-179">`Path``KeyPath`e `Password` para carregar arquivos. *PEM* / *. CRT* e *. Key* .</span><span class="sxs-lookup"><span data-stu-id="481d1-179">`Path`, `KeyPath` and `Password` to load *.pem*/*.crt* and *.key* files.</span></span>
* <span data-ttu-id="481d1-180">`Subject` e `Store` para carregar do repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="481d1-180">`Subject` and `Store` to load from the certificate store.</span></span>

<span data-ttu-id="481d1-181">Por exemplo, o `Certificates:Default` certificado pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="481d1-181">For example, the `Certificates:Default` certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a><span data-ttu-id="481d1-182">ConfigurationLoader</span><span class="sxs-lookup"><span data-stu-id="481d1-182">ConfigurationLoader</span></span>

<span data-ttu-id="481d1-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="481d1-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="481d1-184">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="481d1-184">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span>

* <span data-ttu-id="481d1-185">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="481d1-185">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="481d1-186">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="481d1-186">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="481d1-187">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="481d1-187">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="481d1-188">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="481d1-188">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="481d1-189">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="481d1-189">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="481d1-190">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="481d1-190">These overloads don't use names and only consume default settings from configuration.</span></span>

### <a name="change-the-defaults-in-code"></a><span data-ttu-id="481d1-191">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="481d1-191">Change the defaults in code</span></span>

<span data-ttu-id="481d1-192">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="481d1-192">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="481d1-193">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="481d1-193">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a><span data-ttu-id="481d1-194">Configurar pontos de extremidade usando o Indicação de Nome de Servidor</span><span class="sxs-lookup"><span data-stu-id="481d1-194">Configure endpoints using Server Name Indication</span></span>

<span data-ttu-id="481d1-195">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="481d1-195">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="481d1-196">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="481d1-196">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="481d1-197">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="481d1-197">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="481d1-198">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="481d1-198">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="481d1-199">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="481d1-199">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span> <span data-ttu-id="481d1-200">O código de retorno de chamada a seguir pode ser usado na `ConfigureWebHostDefaults` chamada de método do arquivo *Program.cs* de um projeto:</span><span class="sxs-lookup"><span data-stu-id="481d1-200">The following callback code can be used in the `ConfigureWebHostDefaults` method call of a project's *Program.cs* file:</span></span>

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

<span data-ttu-id="481d1-201">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="481d1-201">SNI support requires:</span></span>

* <span data-ttu-id="481d1-202">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="481d1-202">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="481d1-203">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="481d1-203">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="481d1-204">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="481d1-204">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="481d1-205">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="481d1-205">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="481d1-206">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="481d1-206">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

## <a name="ssltls-protocols"></a><span data-ttu-id="481d1-207">Protocolos SSL/TLS</span><span class="sxs-lookup"><span data-stu-id="481d1-207">SSL/TLS Protocols</span></span>

<span data-ttu-id="481d1-208">Os protocolos SSL são protocolos usados para criptografar e descriptografar o tráfego entre dois pares, tradicionalmente um cliente e um servidor.</span><span class="sxs-lookup"><span data-stu-id="481d1-208">SSL Protocols are protocols used for encrypting and decrypting traffic between two peers, traditionally a client and a server.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

<span data-ttu-id="481d1-209">O valor padrão, `SslProtocols.None` , faz com que o Kestrel use os padrões do sistema operacional para escolher o melhor protocolo.</span><span class="sxs-lookup"><span data-stu-id="481d1-209">The default value, `SslProtocols.None`, causes Kestrel to use the operating system defaults to choose the best protocol.</span></span> <span data-ttu-id="481d1-210">A menos que você tenha um motivo específico para selecionar um protocolo, use o padrão.</span><span class="sxs-lookup"><span data-stu-id="481d1-210">Unless you have a specific reason to select a protocol, use the default.</span></span>
## <a name="connection-logging"></a><span data-ttu-id="481d1-211">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="481d1-211">Connection logging</span></span>

<span data-ttu-id="481d1-212">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="481d1-212">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="481d1-213">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="481d1-213">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="481d1-214">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="481d1-214">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="481d1-215">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="481d1-215">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span> <span data-ttu-id="481d1-216">Este é o middleware de [conexão](#connection-middleware)interno.</span><span class="sxs-lookup"><span data-stu-id="481d1-216">This is built-in [Connection Middleware](#connection-middleware).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="481d1-217">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="481d1-217">Bind to a TCP socket</span></span>

<span data-ttu-id="481d1-218">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="481d1-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="481d1-219">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="481d1-219">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="481d1-220">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="481d1-220">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a><span data-ttu-id="481d1-221">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="481d1-221">Bind to a Unix socket</span></span>

<span data-ttu-id="481d1-222">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="481d1-222">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="481d1-223">No arquivo de configuração Nginx, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="481d1-223">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="481d1-224">`{KESTREL SOCKET}` é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="481d1-224">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="481d1-225">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="481d1-225">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

## <a name="port-0"></a><span data-ttu-id="481d1-226">Porta 0</span><span class="sxs-lookup"><span data-stu-id="481d1-226">Port 0</span></span>

<span data-ttu-id="481d1-227">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="481d1-227">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="481d1-228">O exemplo a seguir mostra como determinar qual porta Kestrel ligada no tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="481d1-228">The following example shows how to determine which port Kestrel bound at runtime:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="481d1-229">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="481d1-229">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a><span data-ttu-id="481d1-230">Limitações</span><span class="sxs-lookup"><span data-stu-id="481d1-230">Limitations</span></span>

<span data-ttu-id="481d1-231">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="481d1-231">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* <span data-ttu-id="481d1-232">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="481d1-232">`--urls` command-line argument</span></span>
* <span data-ttu-id="481d1-233">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="481d1-233">`urls` host configuration key</span></span>
* <span data-ttu-id="481d1-234">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="481d1-234">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="481d1-235">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="481d1-235">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="481d1-236">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="481d1-236">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="481d1-237">O HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando `KestrelServerOptions` configuração ou um arquivo de configuração, como mostrado anteriormente neste artigo).</span><span class="sxs-lookup"><span data-stu-id="481d1-237">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this article).</span></span>
* <span data-ttu-id="481d1-238">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="481d1-238">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

## <a name="iis-endpoint-configuration"></a><span data-ttu-id="481d1-239">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="481d1-239">IIS endpoint configuration</span></span>

<span data-ttu-id="481d1-240">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="481d1-240">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="481d1-241">Para obter mais informações, consulte [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="481d1-241">For more information, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="listenoptionsprotocols"></a><span data-ttu-id="481d1-242">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="481d1-242">ListenOptions.Protocols</span></span>

<span data-ttu-id="481d1-243">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="481d1-243">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="481d1-244">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="481d1-244">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="481d1-245">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="481d1-245">`HttpProtocols` enum value</span></span> | <span data-ttu-id="481d1-246">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="481d1-246">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="481d1-247">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="481d1-247">HTTP/1.1 only.</span></span> <span data-ttu-id="481d1-248">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="481d1-248">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="481d1-249">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="481d1-249">HTTP/2 only.</span></span> <span data-ttu-id="481d1-250">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="481d1-250">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="481d1-251">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="481d1-251">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="481d1-252">O HTTP/2 requer que o cliente selecione HTTP/2 no handshake de [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="481d1-252">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="481d1-253">O `ListenOptions.Protocols` valor padrão para qualquer ponto de extremidade é `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="481d1-253">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="481d1-254">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="481d1-254">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="481d1-255">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="481d1-255">TLS version 1.2 or later</span></span>
* <span data-ttu-id="481d1-256">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="481d1-256">Renegotiation disabled</span></span>
* <span data-ttu-id="481d1-257">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="481d1-257">Compression disabled</span></span>
* <span data-ttu-id="481d1-258">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="481d1-258">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="481d1-259">Diffie-Hellman de curva elíptica (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="481d1-259">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="481d1-260">Diffie-Hellman de campo finito (DHE) &lbrack; `TLS12` &rbrack; : mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="481d1-260">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="481d1-261">Conjunto de codificação não proibido.</span><span class="sxs-lookup"><span data-stu-id="481d1-261">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="481d1-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 tem &lbrack; `FIPS186` &rbrack; suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="481d1-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="481d1-263">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="481d1-263">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="481d1-264">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="481d1-264">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="481d1-265">No Linux, <xref:System.Net.Security.CipherSuitesPolicy> o pode ser usado para filtrar os Handshakes de TLS em uma base por conexão:</span><span class="sxs-lookup"><span data-stu-id="481d1-265">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a><span data-ttu-id="481d1-266">Middleware de conexão</span><span class="sxs-lookup"><span data-stu-id="481d1-266">Connection Middleware</span></span>

<span data-ttu-id="481d1-267">O middleware de conexão personalizada pode filtrar os Handshakes TLS em uma base por conexão para codificações específicas, se necessário.</span><span class="sxs-lookup"><span data-stu-id="481d1-267">Custom connection middleware can filter TLS handshakes on a per-connection basis for specific ciphers if necessary.</span></span>

<span data-ttu-id="481d1-268">O exemplo a seguir gera <xref:System.NotSupportedException> um algoritmo de codificação para o qual o aplicativo não dá suporte.</span><span class="sxs-lookup"><span data-stu-id="481d1-268">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="481d1-269">Como alternativa, defina e compare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) com uma lista de pacotes de codificação aceitáveis.</span><span class="sxs-lookup"><span data-stu-id="481d1-269">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="481d1-270">Nenhuma criptografia é usada com um algoritmo de codificação [CipherAlgorithmType. NULL](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="481d1-270">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="481d1-271">A filtragem de conexão também pode ser configurada por meio de um <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="481d1-271">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a><span data-ttu-id="481d1-272">Definir o protocolo HTTP com base na configuração</span><span class="sxs-lookup"><span data-stu-id="481d1-272">Set the HTTP protocol from configuration</span></span>

<span data-ttu-id="481d1-273">`CreateDefaultBuilder` chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="481d1-273">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="481d1-274">O exemplo a seguir *appsettings.json* estabelece http/1.1 como o protocolo de conexão padrão para todos os pontos de extremidade:</span><span class="sxs-lookup"><span data-stu-id="481d1-274">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="481d1-275">O exemplo a seguir *appsettings.json* estabelece o protocolo de conexão HTTP/1.1 para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="481d1-275">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="481d1-276">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="481d1-276">Protocols specified in code override values set by configuration.</span></span>

## <a name="url-prefixes"></a><span data-ttu-id="481d1-277">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="481d1-277">URL prefixes</span></span>

<span data-ttu-id="481d1-278">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="481d1-278">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="481d1-279">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="481d1-279">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="481d1-280">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="481d1-280">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="481d1-281">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="481d1-281">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="481d1-282">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="481d1-282">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="481d1-283">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="481d1-283">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="481d1-284">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="481d1-284">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="481d1-285">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="481d1-285">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="481d1-286">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="481d1-286">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="481d1-287">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="481d1-287">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="481d1-288">Para associar nomes de host diferentes a diferentes aplicativos de ASP.NET Core na mesma porta, use [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="481d1-288">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server.</span></span> <span data-ttu-id="481d1-289">Os exemplos de servidor proxy reverso incluem IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="481d1-289">Reverse proxy server examples include IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="481d1-290">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="481d1-290">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

* <span data-ttu-id="481d1-291">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="481d1-291">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="481d1-292">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="481d1-292">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="481d1-293">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="481d1-293">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="481d1-294">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="481d1-294">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>
