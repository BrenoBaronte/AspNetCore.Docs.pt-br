---
title: Hospedando ASP.NET Core imagens com o Docker via HTTPS
author: rick-anderson
description: Saiba como hospedar ASP.NET Core imagens com o Docker via HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: a4aac2ce06fee20bdef157efc361f3099a217b1a
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332148"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="b3238-103">Hospedando ASP.NET Core imagens com o Docker via HTTPS</span><span class="sxs-lookup"><span data-stu-id="b3238-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="b3238-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b3238-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b3238-105">O ASP.NET Core usa [https por padrão](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="b3238-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="b3238-106">O [https](https://en.wikipedia.org/wiki/HTTPS) depende de [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) para confiança, identidade e criptografia.</span><span class="sxs-lookup"><span data-stu-id="b3238-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="b3238-107">Este documento explica como executar imagens de contêiner predefinidas com HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b3238-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="b3238-108">Consulte [desenvolvendo aplicativos ASP.NET Core com o Docker sobre HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b3238-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="b3238-109">Este exemplo requer o [docker 17, 6](https://docs.docker.com/release-notes/docker-ce) ou posterior do [cliente do Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="b3238-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b3238-110">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="b3238-110">Prerequisites</span></span>

<span data-ttu-id="b3238-111">O [SDK do .NET Core 2,2](https://dotnet.microsoft.com/download) ou posterior é necessário para algumas das instruções neste documento.</span><span class="sxs-lookup"><span data-stu-id="b3238-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="b3238-112">Certificados</span><span class="sxs-lookup"><span data-stu-id="b3238-112">Certificates</span></span>

<span data-ttu-id="b3238-113">Um certificado de uma [autoridade de certificação](https://wikipedia.org/wiki/Certificate_authority) é necessário para [Hospedagem de produção](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) para um domínio.</span><span class="sxs-lookup"><span data-stu-id="b3238-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="b3238-114">[Let's Encrypt](https://letsencrypt.org/) é uma autoridade de certificação que oferece certificados gratuitos.</span><span class="sxs-lookup"><span data-stu-id="b3238-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="b3238-115">Este documento usa [certificados de desenvolvimento autoassinados](https://en.wikipedia.org/wiki/Self-signed_certificate) para hospedar imagens predefinidas `localhost` .</span><span class="sxs-lookup"><span data-stu-id="b3238-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="b3238-116">As instruções são semelhantes ao uso de certificados de produção.</span><span class="sxs-lookup"><span data-stu-id="b3238-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="b3238-117">Use o [desenvolvimento de dotnet-](/dotnet/core/additional-tools/self-signed-certificates-guide) certificados para criar certificados autoassinados para desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="b3238-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="b3238-118">Para certificados de produção:</span><span class="sxs-lookup"><span data-stu-id="b3238-118">For production certs:</span></span>

* <span data-ttu-id="b3238-119">A `dotnet dev-certs` ferramenta não é necessária.</span><span class="sxs-lookup"><span data-stu-id="b3238-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="b3238-120">Os certificados não precisam ser armazenados no local usado nas instruções.</span><span class="sxs-lookup"><span data-stu-id="b3238-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="b3238-121">Qualquer local deve funcionar, embora o armazenamento de certificados no diretório do site não seja recomendado.</span><span class="sxs-lookup"><span data-stu-id="b3238-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="b3238-122">As instruções contidas na seção a seguir montam os certificados de montagem em contêineres usando a opção de linha de comando do Docker `-v` .</span><span class="sxs-lookup"><span data-stu-id="b3238-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="b3238-123">Você pode adicionar certificados em imagens de contêiner com um `COPY` comando em um *Dockerfile*, mas isso não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="b3238-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="b3238-124">A cópia de certificados em uma imagem não é recomendada pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="b3238-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="b3238-125">Torna difícil usar a mesma imagem para teste com certificados de desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="b3238-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="b3238-126">Dificulta o uso da mesma imagem para hospedagem com certificados de produção.</span><span class="sxs-lookup"><span data-stu-id="b3238-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="b3238-127">Há um risco significativo de divulgação de certificado.</span><span class="sxs-lookup"><span data-stu-id="b3238-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="b3238-128">Executando imagens de contêiner predefinidas com HTTPS</span><span class="sxs-lookup"><span data-stu-id="b3238-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="b3238-129">Use as instruções a seguir para a configuração do seu sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="b3238-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="b3238-130">Windows usando contêineres do Linux</span><span class="sxs-lookup"><span data-stu-id="b3238-130">Windows using Linux containers</span></span>

<span data-ttu-id="b3238-131">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="b3238-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="b3238-132">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="b3238-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="b3238-133">Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b3238-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="b3238-134">Ao usar o [PowerShell](/powershell/scripting/overview), substitua `%USERPROFILE%` por `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="b3238-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="b3238-135">A senha deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="b3238-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="b3238-136">Observação: o certificado nesse caso deve ser um `.pfx` arquivo.</span><span class="sxs-lookup"><span data-stu-id="b3238-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="b3238-137">Utilizar um `.crt` arquivo ou `.key` com ou sem a senha não tem suporte com o contêiner de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b3238-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="b3238-138">Por exemplo, ao especificar um `.crt` arquivo, o contêiner pode retornar mensagens de erro como "o modo de servidor SSL deve usar um certificado com a chave privada associada.".</span><span class="sxs-lookup"><span data-stu-id="b3238-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="b3238-139">Ao usar o [WSL](/windows/wsl/about), valide o caminho de montagem para garantir que o certificado seja carregado corretamente.</span><span class="sxs-lookup"><span data-stu-id="b3238-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="b3238-140">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="b3238-140">macOS or Linux</span></span>

<span data-ttu-id="b3238-141">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="b3238-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="b3238-142">`dotnet dev-certs https --trust` Só tem suporte no macOS e no Windows.</span><span class="sxs-lookup"><span data-stu-id="b3238-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="b3238-143">Você precisa confiar em certificados no Linux na forma com que a sua distribuição dá suporte.</span><span class="sxs-lookup"><span data-stu-id="b3238-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="b3238-144">É provável que você precise confiar no certificado em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="b3238-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="b3238-145">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="b3238-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="b3238-146">Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="b3238-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="b3238-147">A senha deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="b3238-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="b3238-148">Windows usando contêineres do Windows</span><span class="sxs-lookup"><span data-stu-id="b3238-148">Windows using Windows containers</span></span>

<span data-ttu-id="b3238-149">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="b3238-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="b3238-150">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="b3238-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="b3238-151">Ao usar o [PowerShell](/powershell/scripting/overview), substitua `%USERPROFILE%` por `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="b3238-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="b3238-152">Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="b3238-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="b3238-153">A senha deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="b3238-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="b3238-154">Ao usar o [PowerShell](/powershell/scripting/overview), substitua `%USERPROFILE%` por `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="b3238-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
