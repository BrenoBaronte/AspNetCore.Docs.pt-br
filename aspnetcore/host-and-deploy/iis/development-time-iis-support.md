---
title: Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core
author: rick-anderson
description: Descubra o suporte para depuração de aplicativos do ASP.NET Core durante a execução com IIS no Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 5636abef928ed8a8122ec907ef025d55baa65127
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635353"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="80e2c-103">Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80e2c-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="80e2c-104">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="80e2c-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="80e2c-105">Este artigo descreve o suporte do [Visual Studio](https://visualstudio.microsoft.com) para a depuração de aplicativos do ASP.NET Core em execução com o IIS no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="80e2c-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="80e2c-106">Este tópico orienta como habilitar esse cenário e configurar um projeto.</span><span class="sxs-lookup"><span data-stu-id="80e2c-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80e2c-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="80e2c-107">Prerequisites</span></span>

* [<span data-ttu-id="80e2c-108">Visual Studio para Windows</span><span class="sxs-lookup"><span data-stu-id="80e2c-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="80e2c-109">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="80e2c-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="80e2c-110">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="80e2c-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="80e2c-111">Certificado de segurança X.509 (para o suporte a HTTPS)</span><span class="sxs-lookup"><span data-stu-id="80e2c-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="80e2c-112">Habilitar o IIS</span><span class="sxs-lookup"><span data-stu-id="80e2c-112">Enable IIS</span></span>

1. <span data-ttu-id="80e2c-113">No Windows, navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="80e2c-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="80e2c-114">Selecione a caixa de seleção **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="80e2c-115">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-115">Select **OK**.</span></span>

<span data-ttu-id="80e2c-116">A instalação do IIS pode exigir uma reinicialização do sistema.</span><span class="sxs-lookup"><span data-stu-id="80e2c-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="80e2c-117">Configurar o IIS</span><span class="sxs-lookup"><span data-stu-id="80e2c-117">Configure IIS</span></span>

<span data-ttu-id="80e2c-118">O IIS deve ter um site configurado com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="80e2c-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="80e2c-119">**Nome do host**: normalmente, o **site padrão** é usado com um **nome de host** de `localhost` .</span><span class="sxs-lookup"><span data-stu-id="80e2c-119">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="80e2c-120">No entanto, qualquer site válido do IIS com um nome de host exclusivo funciona.</span><span class="sxs-lookup"><span data-stu-id="80e2c-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="80e2c-121">**Associação do site**</span><span class="sxs-lookup"><span data-stu-id="80e2c-121">**Site Binding**</span></span>
  * <span data-ttu-id="80e2c-122">Para aplicativos que exijam HTTPS, crie uma associação à porta 443 com um certificado.</span><span class="sxs-lookup"><span data-stu-id="80e2c-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="80e2c-123">Tipicamente, o **Certificado de Desenvolvimento do IIS Express** é usado, mas qualquer certificado válido funciona.</span><span class="sxs-lookup"><span data-stu-id="80e2c-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="80e2c-124">Para aplicativos que usam HTTP, confirme a existência de uma associação à porta 80 ou crie uma para um novo site.</span><span class="sxs-lookup"><span data-stu-id="80e2c-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="80e2c-125">Uso de uma associação simples para HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80e2c-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="80e2c-126">**Não há suporte para a associação simultânea às portas HTTP e HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="80e2c-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="80e2c-127">Habilitar o suporte ao IIS no tempo de desenvolvimento no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="80e2c-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="80e2c-128">Inicie o Instalador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="80e2c-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="80e2c-129">Selecione **Modificar** para a instalação do Visual Studio que você planeja usar para o suporte de tempo de desenvolvimento do IIS.</span><span class="sxs-lookup"><span data-stu-id="80e2c-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="80e2c-130">Para a carga de trabalho de **desenvolvimento da web e ASP.NET**, localize e instale o componente de**suporte IIS ao tempo de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="80e2c-131">O componente está listado na seção **Opcional**, em **Suporte IIS ao tempo de desenvolvimento** no painel **Detalhes da instalação** à direita das cargas de trabalho.</span><span class="sxs-lookup"><span data-stu-id="80e2c-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="80e2c-132">O componente instala o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que é um módulo nativo do IIS necessário para executar aplicativos ASP.NET Core com o IIS.</span><span class="sxs-lookup"><span data-stu-id="80e2c-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="80e2c-133">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="80e2c-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="80e2c-134">Redirecionamento para HTTPS</span><span class="sxs-lookup"><span data-stu-id="80e2c-134">HTTPS redirection</span></span>

<span data-ttu-id="80e2c-135">Para um novo projeto que exija HTTPS, selecione a caixa de seleção para **Configurar para HTTPS** na janela **Criar um novo Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="80e2c-136">A marcação da caixa de seleção adiciona ao aplicativo um [Redirecionamento de HTTPS e Middleware HSTS](xref:security/enforcing-ssl) quando ele é criado.</span><span class="sxs-lookup"><span data-stu-id="80e2c-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="80e2c-137">Para um projeto existente que exija HTTPS, use o Redirecionamento de HTTPS e Middleware HSTS em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="80e2c-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="80e2c-138">Para obter mais informações, consulte <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="80e2c-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="80e2c-139">Para um projeto que usa HTTP, o [Redirecionamento de HTTPS e o Middleware HSTS](xref:security/enforcing-ssl) não são adicionados ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80e2c-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="80e2c-140">Nenhuma configuração de aplicativo é necessária.</span><span class="sxs-lookup"><span data-stu-id="80e2c-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="80e2c-141">Perfil de inicialização do IIS</span><span class="sxs-lookup"><span data-stu-id="80e2c-141">IIS launch profile</span></span>

<span data-ttu-id="80e2c-142">Crie um novo perfil de inicialização para adicionar suporte ao IIS no tempo de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="80e2c-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="80e2c-143">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="80e2c-144">Selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-144">Select **Properties**.</span></span> <span data-ttu-id="80e2c-145">Abra a guia **depurar** .</span><span class="sxs-lookup"><span data-stu-id="80e2c-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="80e2c-146">Para **Perfil**, selecione o botão **Novo**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="80e2c-147">Nomeie o perfil "IIS" na janela pop-up.</span><span class="sxs-lookup"><span data-stu-id="80e2c-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="80e2c-148">Selecione **OK** para criar o perfil.</span><span class="sxs-lookup"><span data-stu-id="80e2c-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="80e2c-149">Para a configuração **Iniciar**, selecione **IIS** da lista.</span><span class="sxs-lookup"><span data-stu-id="80e2c-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="80e2c-150">Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="80e2c-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="80e2c-151">Quando o aplicativo exigir HTTPS, use um ponto de extremidade HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="80e2c-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="80e2c-152">Para HTTP, use um ponto de extremidade HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="80e2c-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="80e2c-153">Forneça o mesmo nome de host e porta usados pela [configuração do IIS especificada anteriormente](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="80e2c-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="80e2c-154">Forneça o nome do aplicativo no final da URL.</span><span class="sxs-lookup"><span data-stu-id="80e2c-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="80e2c-155">Por exemplo, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) são URLs de ponto de extremidade válidas.</span><span class="sxs-lookup"><span data-stu-id="80e2c-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="80e2c-156">Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="80e2c-157">Forneça uma variável de ambiente com um **Nome** de `ASPNETCORE_ENVIRONMENT` e um **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="80e2c-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="80e2c-158">Na área de **Configurações do servidor Web**, defina a **URL do aplicativo** com o mesmo valor usado para a URL de ponto de extremidade **Iniciar navegador**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="80e2c-159">Para a configuração de **Modelo de hospedagem** no Visual Studio 2019 ou posterior, selecione **Padrão** para usar o modelo de hospedagem usado pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="80e2c-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="80e2c-160">Se o projeto definir a propriedade `<AspNetCoreHostingModel>` no arquivo de projeto, o valor da propriedade (`InProcess` ou `OutOfProcess`) será usado.</span><span class="sxs-lookup"><span data-stu-id="80e2c-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="80e2c-161">Se a propriedade não estiver presente, o modelo do aplicativo de hospedagem padrão será usado, que está em processo.</span><span class="sxs-lookup"><span data-stu-id="80e2c-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="80e2c-162">Se o aplicativo exigir uma configuração explícita de modelo de hospedagem diferente da do modelo de hospedagem normal do aplicativo, defina o **Modelo de hospedagem** para `In Process` ou `Out Of Process`, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="80e2c-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="80e2c-163">Salve o perfil.</span><span class="sxs-lookup"><span data-stu-id="80e2c-163">Save the profile.</span></span>

<span data-ttu-id="80e2c-164">Quando não estiver usando o Visual Studio, adicione manualmente um perfil de inicialização ao arquivo [launchSettings.json](https://json.schemastore.org/launchsettings) na pasta *Propriedades*.</span><span class="sxs-lookup"><span data-stu-id="80e2c-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="80e2c-165">O exemplo a seguir configura o perfil para usar o protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="80e2c-165">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="80e2c-166">Confirme se os pontos de extremidade `applicationUrl` e `launchUrl` correspondem e usam o mesmo protocolo que a configuração de associação do IIS, seja HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80e2c-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="80e2c-167">Executar o projeto</span><span class="sxs-lookup"><span data-stu-id="80e2c-167">Run the project</span></span>

<span data-ttu-id="80e2c-168">Execute o Visual Studio como um administrador:</span><span class="sxs-lookup"><span data-stu-id="80e2c-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="80e2c-169">Confirme se a lista de lista suspensa de configuração de compilação está definida para **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="80e2c-170">Defina o [botão Iniciar Depuração](/visualstudio/debugger/debugger-feature-tour) para o perfil do **IIS** e selecione o botão para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80e2c-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="80e2c-171">O Visual Studio poderá solicitar uma reinicialização se não estiver executando como administrador.</span><span class="sxs-lookup"><span data-stu-id="80e2c-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="80e2c-172">Se solicitado, reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="80e2c-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="80e2c-173">Se for usado um certificado de desenvolvimento não confiável, o navegador poderá exigir a criação de uma exceção para o certificado não confiável.</span><span class="sxs-lookup"><span data-stu-id="80e2c-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="80e2c-174">A depuração de uma configuração de Compilação de versão com [Apenas Meu Código](/visualstudio/debugger/just-my-code) e otimizações de compilador resulta em uma experiência inadequada.</span><span class="sxs-lookup"><span data-stu-id="80e2c-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="80e2c-175">Por exemplo, os pontos de interrupção não são atingidos.</span><span class="sxs-lookup"><span data-stu-id="80e2c-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80e2c-176">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="80e2c-176">Additional resources</span></span>

* [<span data-ttu-id="80e2c-177">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="80e2c-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="80e2c-178">Este artigo descreve o suporte do [Visual Studio](https://visualstudio.microsoft.com) para a depuração de aplicativos do ASP.NET Core em execução com o IIS no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="80e2c-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="80e2c-179">Este tópico orienta como habilitar esse cenário e configurar um projeto.</span><span class="sxs-lookup"><span data-stu-id="80e2c-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80e2c-180">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="80e2c-180">Prerequisites</span></span>

* [<span data-ttu-id="80e2c-181">Visual Studio para Windows</span><span class="sxs-lookup"><span data-stu-id="80e2c-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="80e2c-182">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="80e2c-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="80e2c-183">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="80e2c-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="80e2c-184">Certificado de segurança X.509 (para o suporte a HTTPS)</span><span class="sxs-lookup"><span data-stu-id="80e2c-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="80e2c-185">Habilitar o IIS</span><span class="sxs-lookup"><span data-stu-id="80e2c-185">Enable IIS</span></span>

1. <span data-ttu-id="80e2c-186">No Windows, navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="80e2c-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="80e2c-187">Selecione a caixa de seleção **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="80e2c-188">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-188">Select **OK**.</span></span>

<span data-ttu-id="80e2c-189">A instalação do IIS pode exigir uma reinicialização do sistema.</span><span class="sxs-lookup"><span data-stu-id="80e2c-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="80e2c-190">Configurar o IIS</span><span class="sxs-lookup"><span data-stu-id="80e2c-190">Configure IIS</span></span>

<span data-ttu-id="80e2c-191">O IIS deve ter um site configurado com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="80e2c-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="80e2c-192">**Nome do host**: normalmente, o **site padrão** é usado com um **nome de host** de `localhost` .</span><span class="sxs-lookup"><span data-stu-id="80e2c-192">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="80e2c-193">No entanto, qualquer site válido do IIS com um nome de host exclusivo funciona.</span><span class="sxs-lookup"><span data-stu-id="80e2c-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="80e2c-194">**Associação do site**</span><span class="sxs-lookup"><span data-stu-id="80e2c-194">**Site Binding**</span></span>
  * <span data-ttu-id="80e2c-195">Para aplicativos que exijam HTTPS, crie uma associação à porta 443 com um certificado.</span><span class="sxs-lookup"><span data-stu-id="80e2c-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="80e2c-196">Tipicamente, o **Certificado de Desenvolvimento do IIS Express** é usado, mas qualquer certificado válido funciona.</span><span class="sxs-lookup"><span data-stu-id="80e2c-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="80e2c-197">Para aplicativos que usam HTTP, confirme a existência de uma associação à porta 80 ou crie uma para um novo site.</span><span class="sxs-lookup"><span data-stu-id="80e2c-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="80e2c-198">Uso de uma associação simples para HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80e2c-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="80e2c-199">**Não há suporte para a associação simultânea às portas HTTP e HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="80e2c-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="80e2c-200">Habilitar o suporte ao IIS no tempo de desenvolvimento no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="80e2c-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="80e2c-201">Inicie o Instalador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="80e2c-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="80e2c-202">Selecione **Modificar** para a instalação do Visual Studio que você planeja usar para o suporte de tempo de desenvolvimento do IIS.</span><span class="sxs-lookup"><span data-stu-id="80e2c-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="80e2c-203">Para a carga de trabalho de **desenvolvimento da web e ASP.NET**, localize e instale o componente de**suporte IIS ao tempo de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="80e2c-204">O componente está listado na seção **Opcional**, em **Suporte IIS ao tempo de desenvolvimento** no painel **Detalhes da instalação** à direita das cargas de trabalho.</span><span class="sxs-lookup"><span data-stu-id="80e2c-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="80e2c-205">O componente instala o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que é um módulo nativo do IIS necessário para executar aplicativos ASP.NET Core com o IIS.</span><span class="sxs-lookup"><span data-stu-id="80e2c-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="80e2c-206">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="80e2c-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="80e2c-207">Redirecionamento para HTTPS</span><span class="sxs-lookup"><span data-stu-id="80e2c-207">HTTPS redirection</span></span>

<span data-ttu-id="80e2c-208">Para um novo projeto que exija HTTPS, selecione a caixa de seleção para **Configurar para HTTPS** na janela **Criar um novo Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="80e2c-209">A marcação da caixa de seleção adiciona ao aplicativo um [Redirecionamento de HTTPS e Middleware HSTS](xref:security/enforcing-ssl) quando ele é criado.</span><span class="sxs-lookup"><span data-stu-id="80e2c-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="80e2c-210">Para um projeto existente que exija HTTPS, use o Redirecionamento de HTTPS e Middleware HSTS em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="80e2c-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="80e2c-211">Para obter mais informações, consulte <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="80e2c-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="80e2c-212">Para um projeto que usa HTTP, o [Redirecionamento de HTTPS e o Middleware HSTS](xref:security/enforcing-ssl) não são adicionados ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80e2c-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="80e2c-213">Nenhuma configuração de aplicativo é necessária.</span><span class="sxs-lookup"><span data-stu-id="80e2c-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="80e2c-214">Perfil de inicialização do IIS</span><span class="sxs-lookup"><span data-stu-id="80e2c-214">IIS launch profile</span></span>

<span data-ttu-id="80e2c-215">Crie um novo perfil de inicialização para adicionar suporte ao IIS no tempo de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="80e2c-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="80e2c-216">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="80e2c-217">Selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-217">Select **Properties**.</span></span> <span data-ttu-id="80e2c-218">Abra a guia **depurar** .</span><span class="sxs-lookup"><span data-stu-id="80e2c-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="80e2c-219">Para **Perfil**, selecione o botão **Novo**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="80e2c-220">Nomeie o perfil "IIS" na janela pop-up.</span><span class="sxs-lookup"><span data-stu-id="80e2c-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="80e2c-221">Selecione **OK** para criar o perfil.</span><span class="sxs-lookup"><span data-stu-id="80e2c-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="80e2c-222">Para a configuração **Iniciar**, selecione **IIS** da lista.</span><span class="sxs-lookup"><span data-stu-id="80e2c-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="80e2c-223">Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="80e2c-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="80e2c-224">Quando o aplicativo exigir HTTPS, use um ponto de extremidade HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="80e2c-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="80e2c-225">Para HTTP, use um ponto de extremidade HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="80e2c-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="80e2c-226">Forneça o mesmo nome de host e porta usados pela [configuração do IIS especificada anteriormente](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="80e2c-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="80e2c-227">Forneça o nome do aplicativo no final da URL.</span><span class="sxs-lookup"><span data-stu-id="80e2c-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="80e2c-228">Por exemplo, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) são URLs de ponto de extremidade válidas.</span><span class="sxs-lookup"><span data-stu-id="80e2c-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="80e2c-229">Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="80e2c-230">Forneça uma variável de ambiente com um **Nome** de `ASPNETCORE_ENVIRONMENT` e um **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="80e2c-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="80e2c-231">Na área de **Configurações do servidor Web**, defina a **URL do aplicativo** com o mesmo valor usado para a URL de ponto de extremidade **Iniciar navegador**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="80e2c-232">Para a configuração de **Modelo de hospedagem** no Visual Studio 2019 ou posterior, selecione **Padrão** para usar o modelo de hospedagem usado pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="80e2c-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="80e2c-233">Se o projeto definir a propriedade `<AspNetCoreHostingModel>` no arquivo de projeto, o valor da propriedade (`InProcess` ou `OutOfProcess`) será usado.</span><span class="sxs-lookup"><span data-stu-id="80e2c-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="80e2c-234">Se a propriedade não estiver presente, o modelo do aplicativo de hospedagem padrão será usado, que está fora de processo.</span><span class="sxs-lookup"><span data-stu-id="80e2c-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="80e2c-235">Se o aplicativo exigir uma configuração explícita de modelo de hospedagem diferente da do modelo de hospedagem normal do aplicativo, defina o **Modelo de hospedagem** para `In Process` ou `Out Of Process`, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="80e2c-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="80e2c-236">Salve o perfil.</span><span class="sxs-lookup"><span data-stu-id="80e2c-236">Save the profile.</span></span>

<span data-ttu-id="80e2c-237">Quando não estiver usando o Visual Studio, adicione manualmente um perfil de inicialização ao arquivo [launchSettings.json](https://json.schemastore.org/launchsettings) na pasta *Propriedades*.</span><span class="sxs-lookup"><span data-stu-id="80e2c-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="80e2c-238">O exemplo a seguir configura o perfil para usar o protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="80e2c-238">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="80e2c-239">Confirme se os pontos de extremidade `applicationUrl` e `launchUrl` correspondem e usam o mesmo protocolo que a configuração de associação do IIS, seja HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80e2c-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="80e2c-240">Executar o projeto</span><span class="sxs-lookup"><span data-stu-id="80e2c-240">Run the project</span></span>

<span data-ttu-id="80e2c-241">Execute o Visual Studio como um administrador:</span><span class="sxs-lookup"><span data-stu-id="80e2c-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="80e2c-242">Confirme se a lista de lista suspensa de configuração de compilação está definida para **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="80e2c-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="80e2c-243">Defina o [botão Iniciar Depuração](/visualstudio/debugger/debugger-feature-tour) para o perfil do **IIS** e selecione o botão para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80e2c-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="80e2c-244">O Visual Studio poderá solicitar uma reinicialização se não estiver executando como administrador.</span><span class="sxs-lookup"><span data-stu-id="80e2c-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="80e2c-245">Se solicitado, reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="80e2c-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="80e2c-246">Se for usado um certificado de desenvolvimento não confiável, o navegador poderá exigir a criação de uma exceção para o certificado não confiável.</span><span class="sxs-lookup"><span data-stu-id="80e2c-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="80e2c-247">A depuração de uma configuração de Compilação de versão com [Apenas Meu Código](/visualstudio/debugger/just-my-code) e otimizações de compilador resulta em uma experiência inadequada.</span><span class="sxs-lookup"><span data-stu-id="80e2c-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="80e2c-248">Por exemplo, os pontos de interrupção não são atingidos.</span><span class="sxs-lookup"><span data-stu-id="80e2c-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80e2c-249">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="80e2c-249">Additional resources</span></span>

* [<span data-ttu-id="80e2c-250">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="80e2c-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
