---
title: Hospedar o ASP.NET Core em um serviço Windows
author: rick-anderson
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: 63267bf938c6d16b8a1b13940a4b3f8a02d1a1e4
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252741"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="e4a5e-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="e4a5e-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e4a5e-104">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="e4a5e-105">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="e4a5e-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e4a5e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e4a5e-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e4a5e-107">Prerequisites</span></span>

* [<span data-ttu-id="e4a5e-108">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e4a5e-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="e4a5e-109">PowerShell 6,2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e4a5e-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="e4a5e-110">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="e4a5e-110">Worker Service template</span></span>

<span data-ttu-id="e4a5e-111">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="e4a5e-112">Para usar o modelo como base para um aplicativo de Serviço Windows:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="e4a5e-113">Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="e4a5e-114">Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="e4a5e-115">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-115">App configuration</span></span>

<span data-ttu-id="e4a5e-116">O aplicativo requer uma referência de pacote para [Microsoft. Extensions. host. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="e4a5e-117">`IHostBuilder.UseWindowsService` é chamado ao criar o host.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="e4a5e-118">Se o aplicativo estiver sendo executado como um Serviço Windows, o método:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="e4a5e-119">Define o tempo de vida do host como `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="e4a5e-120">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="e4a5e-121">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="e4a5e-122">Habilita o registro em log no log de eventos:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="e4a5e-123">O nome do aplicativo é usado como o nome de origem padrão.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="e4a5e-124">O nível de log padrão é *aviso* ou superior para um aplicativo com base em um modelo de ASP.NET Core que chama `CreateDefaultBuilder` para criar o host.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="e4a5e-125">Substitua o nível de log padrão pela `Logging:EventLog:LogLevel:Default` chave em *appsettings.json* / *appSettings. { Ambiente}. JSON* ou outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="e4a5e-126">Somente administradores podem criar novas fontes de evento.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="e4a5e-127">Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="e4a5e-128">Em `CreateHostBuilder` *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="e4a5e-129">Os aplicativos de exemplo a seguir acompanham este tópico:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="e4a5e-130">Exemplo de serviço de trabalho em segundo plano: um aplicativo não Web de exemplo baseado no [modelo de serviço de trabalho](#worker-service-template) que usa [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="e4a5e-131">Exemplo de serviço de aplicativo Web: um Razor exemplo de aplicativo Web de páginas que é executado como um serviço do Windows com [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="e4a5e-132">Para obter diretrizes do MVC, consulte os artigos em <xref:mvc/overview> e <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="e4a5e-133">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="e4a5e-133">Deployment type</span></span>

<span data-ttu-id="e4a5e-134">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="e4a5e-135">.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-135">SDK</span></span>

<span data-ttu-id="e4a5e-136">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e4a5e-137">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="e4a5e-138">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="e4a5e-139">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="e4a5e-140">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="e4a5e-141">Se você estiver usando o [SDK da Web](#sdk), um arquivo *web.config* , que normalmente é produzido ao publicar um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="e4a5e-142">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="e4a5e-143">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="e4a5e-144">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="e4a5e-145">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="e4a5e-146">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="e4a5e-147">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="e4a5e-148">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="e4a5e-149">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="e4a5e-150">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="e4a5e-151">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-151">Service user account</span></span>

<span data-ttu-id="e4a5e-152">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="e4a5e-153">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-154">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="e4a5e-155">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="e4a5e-156">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="e4a5e-157">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="e4a5e-158">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="e4a5e-159">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="e4a5e-160">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-160">Log on as a service rights</span></span>

<span data-ttu-id="e4a5e-161">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="e4a5e-162">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="e4a5e-163">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="e4a5e-164">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="e4a5e-165">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="e4a5e-166">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="e4a5e-167">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="e4a5e-168">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-168">Select **Advanced**.</span></span> <span data-ttu-id="e4a5e-169">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-169">Select **Find Now**.</span></span> <span data-ttu-id="e4a5e-170">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-170">Select the user account from the list.</span></span> <span data-ttu-id="e4a5e-171">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-171">Select **OK**.</span></span> <span data-ttu-id="e4a5e-172">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="e4a5e-173">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="e4a5e-174">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="e4a5e-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="e4a5e-175">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-175">Create a service</span></span>

<span data-ttu-id="e4a5e-176">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="e4a5e-177">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="e4a5e-178">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="e4a5e-179">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="e4a5e-180">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="e4a5e-181">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="e4a5e-182">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="e4a5e-183">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="e4a5e-184">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="e4a5e-185">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="e4a5e-186">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="e4a5e-187">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-187">Start a service</span></span>

<span data-ttu-id="e4a5e-188">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-189">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="e4a5e-190">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-190">Determine a service's status</span></span>

<span data-ttu-id="e4a5e-191">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-192">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="e4a5e-193">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-193">Stop a service</span></span>

<span data-ttu-id="e4a5e-194">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="e4a5e-195">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-195">Remove a service</span></span>

<span data-ttu-id="e4a5e-196">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e4a5e-197">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="e4a5e-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e4a5e-198">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="e4a5e-199">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="e4a5e-200">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="e4a5e-200">Configure endpoints</span></span>

<span data-ttu-id="e4a5e-201">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e4a5e-202">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="e4a5e-203">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="e4a5e-204">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="e4a5e-205">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="e4a5e-206">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="e4a5e-207">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-207">Current directory and content root</span></span>

<span data-ttu-id="e4a5e-208">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory%2A> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory%2A> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="e4a5e-209">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="e4a5e-210">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="e4a5e-211">Usar ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="e4a5e-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="e4a5e-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="e4a5e-213">Quando o aplicativo é executado como um serviço, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> o define <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="e4a5e-214">Os arquivos de configurações padrão do aplicativo *appsettings.json* e *appSettings. { Environment}. JSON*, são carregados a partir da raiz do conteúdo do aplicativo chamando [CreateDefaultBuilder durante a construção do host](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="e4a5e-215">Para outros arquivos de configurações carregados pelo código do desenvolvedor no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> , não há necessidade de chamar <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>.</span></span> <span data-ttu-id="e4a5e-216">No exemplo a seguir, o *custom_settings.jsno* arquivo existe na raiz do conteúdo do aplicativo e é carregado sem definir explicitamente um caminho base:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="e4a5e-217">Não tente usar <xref:System.IO.Directory.GetCurrentDirectory%2A> para obter um caminho de recurso porque um aplicativo de serviço do Windows retorna a pasta *C: \\ Windows \\ System32* como seu diretório atual.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory%2A> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="e4a5e-218">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="e4a5e-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="e4a5e-219">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e4a5e-220">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="e4a5e-220">Troubleshoot</span></span>

<span data-ttu-id="e4a5e-221">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="e4a5e-222">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="e4a5e-222">Common errors</span></span>

* <span data-ttu-id="e4a5e-223">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="e4a5e-224">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="e4a5e-225">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="e4a5e-226">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="e4a5e-227">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="e4a5e-228">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="e4a5e-229">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="e4a5e-230">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="e4a5e-231">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="e4a5e-232">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="e4a5e-233">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="e4a5e-234">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="e4a5e-235">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="e4a5e-236">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-236">System and Application Event Logs</span></span>

<span data-ttu-id="e4a5e-237">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="e4a5e-238">Abra o menu Iniciar, procure *Visualizador de eventos* e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e4a5e-239">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e4a5e-240">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="e4a5e-241">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e4a5e-242">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e4a5e-243">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="e4a5e-243">Run the app at a command prompt</span></span>

<span data-ttu-id="e4a5e-244">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="e4a5e-245">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="e4a5e-246">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="e4a5e-247">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="e4a5e-247">Clear package caches</span></span>

<span data-ttu-id="e4a5e-248">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="e4a5e-249">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e4a5e-250">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e4a5e-251">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e4a5e-252">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="e4a5e-253">Limpar caches de pacote também pode ser feito com a ferramenta de [nuget.exe](https://www.nuget.org/downloads) e executar o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e4a5e-254">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="e4a5e-255">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e4a5e-256">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="e4a5e-257">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="e4a5e-257">Slow or hanging app</span></span>

<span data-ttu-id="e4a5e-258">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e4a5e-259">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="e4a5e-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="e4a5e-260">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e4a5e-261">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="e4a5e-262">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="e4a5e-263">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e4a5e-264">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="e4a5e-265">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e4a5e-266">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e4a5e-267">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e4a5e-268">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="e4a5e-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e4a5e-269">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="e4a5e-270">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="e4a5e-270">Analyze the dump</span></span>

<span data-ttu-id="e4a5e-271">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e4a5e-272">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4a5e-273">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e4a5e-273">Additional resources</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="e4a5e-274">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel/endpoints) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* <span data-ttu-id="e4a5e-275">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e4a5e-276">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="e4a5e-277">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="e4a5e-278">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e4a5e-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e4a5e-279">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e4a5e-279">Prerequisites</span></span>

* [<span data-ttu-id="e4a5e-280">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e4a5e-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="e4a5e-281">PowerShell 6,2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e4a5e-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="e4a5e-282">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-282">App configuration</span></span>

<span data-ttu-id="e4a5e-283">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="e4a5e-284">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="e4a5e-285">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="e4a5e-286">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="e4a5e-287">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="e4a5e-288">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="e4a5e-289">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="e4a5e-290">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="e4a5e-291">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="e4a5e-292">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="e4a5e-293">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="e4a5e-294">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="e4a5e-295">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="e4a5e-296">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="e4a5e-297">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="e4a5e-298">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="e4a5e-298">Deployment type</span></span>

<span data-ttu-id="e4a5e-299">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="e4a5e-300">.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-300">SDK</span></span>

<span data-ttu-id="e4a5e-301">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e4a5e-302">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="e4a5e-303">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="e4a5e-304">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="e4a5e-305">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="e4a5e-306">O [RID (identificador de tempo de execução](/dotnet/core/rid-catalog) do Windows) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="e4a5e-307">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="e4a5e-308">A propriedade `<SelfContained>` está definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="e4a5e-309">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="e4a5e-310">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="e4a5e-311">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="e4a5e-312">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="e4a5e-313">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="e4a5e-314">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="e4a5e-315">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="e4a5e-316">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="e4a5e-317">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="e4a5e-318">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="e4a5e-319">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="e4a5e-320">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="e4a5e-321">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-321">Service user account</span></span>

<span data-ttu-id="e4a5e-322">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="e4a5e-323">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-324">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="e4a5e-325">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="e4a5e-326">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="e4a5e-327">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="e4a5e-328">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="e4a5e-329">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="e4a5e-330">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-330">Log on as a service rights</span></span>

<span data-ttu-id="e4a5e-331">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="e4a5e-332">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="e4a5e-333">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="e4a5e-334">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="e4a5e-335">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="e4a5e-336">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="e4a5e-337">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="e4a5e-338">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-338">Select **Advanced**.</span></span> <span data-ttu-id="e4a5e-339">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-339">Select **Find Now**.</span></span> <span data-ttu-id="e4a5e-340">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-340">Select the user account from the list.</span></span> <span data-ttu-id="e4a5e-341">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-341">Select **OK**.</span></span> <span data-ttu-id="e4a5e-342">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="e4a5e-343">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="e4a5e-344">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="e4a5e-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="e4a5e-345">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-345">Create a service</span></span>

<span data-ttu-id="e4a5e-346">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="e4a5e-347">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="e4a5e-348">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-348">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="e4a5e-349">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="e4a5e-350">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="e4a5e-351">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-351">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="e4a5e-352">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-352">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="e4a5e-353">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-353">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="e4a5e-354">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="e4a5e-355">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-355">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="e4a5e-356">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-356">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="e4a5e-357">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-357">Start a service</span></span>

<span data-ttu-id="e4a5e-358">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-359">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="e4a5e-360">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-360">Determine a service's status</span></span>

<span data-ttu-id="e4a5e-361">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-362">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="e4a5e-363">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-363">Stop a service</span></span>

<span data-ttu-id="e4a5e-364">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="e4a5e-365">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-365">Remove a service</span></span>

<span data-ttu-id="e4a5e-366">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="e4a5e-367">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="e4a5e-367">Handle starting and stopping events</span></span>

<span data-ttu-id="e4a5e-368">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="e4a5e-369">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="e4a5e-370">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="e4a5e-371">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="e4a5e-372">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e4a5e-373">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="e4a5e-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e4a5e-374">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="e4a5e-375">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="e4a5e-376">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="e4a5e-376">Configure endpoints</span></span>

<span data-ttu-id="e4a5e-377">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e4a5e-378">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="e4a5e-379">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="e4a5e-380">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="e4a5e-381">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="e4a5e-382">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="e4a5e-383">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-383">Current directory and content root</span></span>

<span data-ttu-id="e4a5e-384">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="e4a5e-385">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="e4a5e-386">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="e4a5e-387">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="e4a5e-388">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="e4a5e-389">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="e4a5e-390">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="e4a5e-391">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="e4a5e-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="e4a5e-392">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e4a5e-393">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="e4a5e-393">Troubleshoot</span></span>

<span data-ttu-id="e4a5e-394">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="e4a5e-395">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="e4a5e-395">Common errors</span></span>

* <span data-ttu-id="e4a5e-396">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="e4a5e-397">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="e4a5e-398">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="e4a5e-399">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="e4a5e-400">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="e4a5e-401">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="e4a5e-402">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="e4a5e-403">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="e4a5e-404">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="e4a5e-405">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="e4a5e-406">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="e4a5e-407">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="e4a5e-408">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="e4a5e-409">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-409">System and Application Event Logs</span></span>

<span data-ttu-id="e4a5e-410">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="e4a5e-411">Abra o menu Iniciar, procure *Visualizador de eventos* e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e4a5e-412">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e4a5e-413">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="e4a5e-414">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e4a5e-415">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e4a5e-416">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="e4a5e-416">Run the app at a command prompt</span></span>

<span data-ttu-id="e4a5e-417">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="e4a5e-418">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="e4a5e-419">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="e4a5e-420">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="e4a5e-420">Clear package caches</span></span>

<span data-ttu-id="e4a5e-421">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="e4a5e-422">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e4a5e-423">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e4a5e-424">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e4a5e-425">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="e4a5e-426">Limpar caches de pacote também pode ser feito com a ferramenta de [nuget.exe](https://www.nuget.org/downloads) e executar o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e4a5e-427">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="e4a5e-428">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e4a5e-429">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="e4a5e-430">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="e4a5e-430">Slow or hanging app</span></span>

<span data-ttu-id="e4a5e-431">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e4a5e-432">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="e4a5e-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="e4a5e-433">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e4a5e-434">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="e4a5e-435">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-435">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="e4a5e-436">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e4a5e-437">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="e4a5e-438">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e4a5e-439">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e4a5e-440">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e4a5e-441">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="e4a5e-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e4a5e-442">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="e4a5e-443">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="e4a5e-443">Analyze the dump</span></span>

<span data-ttu-id="e4a5e-444">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e4a5e-445">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4a5e-446">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e4a5e-446">Additional resources</span></span>

* <span data-ttu-id="e4a5e-447">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e4a5e-448">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="e4a5e-449">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="e4a5e-450">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e4a5e-450">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e4a5e-451">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e4a5e-451">Prerequisites</span></span>

* [<span data-ttu-id="e4a5e-452">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e4a5e-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="e4a5e-453">PowerShell 6,2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e4a5e-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="e4a5e-454">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-454">App configuration</span></span>

<span data-ttu-id="e4a5e-455">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="e4a5e-456">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="e4a5e-457">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="e4a5e-458">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="e4a5e-459">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="e4a5e-460">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="e4a5e-461">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="e4a5e-462">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="e4a5e-463">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="e4a5e-464">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="e4a5e-465">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="e4a5e-466">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="e4a5e-467">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="e4a5e-468">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="e4a5e-469">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="e4a5e-470">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="e4a5e-470">Deployment type</span></span>

<span data-ttu-id="e4a5e-471">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="e4a5e-472">.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-472">SDK</span></span>

<span data-ttu-id="e4a5e-473">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e4a5e-474">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="e4a5e-475">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="e4a5e-476">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="e4a5e-477">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="e4a5e-478">O [RID (identificador de tempo de execução](/dotnet/core/rid-catalog) do Windows) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="e4a5e-479">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="e4a5e-480">A propriedade `<SelfContained>` está definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="e4a5e-481">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="e4a5e-482">A propriedade `<UseAppHost>` está definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="e4a5e-483">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="e4a5e-484">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="e4a5e-485">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="e4a5e-486">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="e4a5e-487">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="e4a5e-488">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="e4a5e-489">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="e4a5e-490">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="e4a5e-491">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="e4a5e-492">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="e4a5e-493">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="e4a5e-494">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="e4a5e-495">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-495">Service user account</span></span>

<span data-ttu-id="e4a5e-496">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="e4a5e-497">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-498">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="e4a5e-499">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="e4a5e-500">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="e4a5e-501">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="e4a5e-502">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="e4a5e-503">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="e4a5e-504">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-504">Log on as a service rights</span></span>

<span data-ttu-id="e4a5e-505">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="e4a5e-506">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="e4a5e-507">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="e4a5e-508">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="e4a5e-509">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="e4a5e-510">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="e4a5e-511">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="e4a5e-512">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-512">Select **Advanced**.</span></span> <span data-ttu-id="e4a5e-513">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-513">Select **Find Now**.</span></span> <span data-ttu-id="e4a5e-514">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-514">Select the user account from the list.</span></span> <span data-ttu-id="e4a5e-515">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-515">Select **OK**.</span></span> <span data-ttu-id="e4a5e-516">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="e4a5e-517">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="e4a5e-518">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="e4a5e-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="e4a5e-519">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-519">Create a service</span></span>

<span data-ttu-id="e4a5e-520">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="e4a5e-521">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="e4a5e-522">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-522">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="e4a5e-523">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="e4a5e-524">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="e4a5e-525">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-525">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="e4a5e-526">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-526">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="e4a5e-527">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-527">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="e4a5e-528">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="e4a5e-529">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-529">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="e4a5e-530">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-530">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="e4a5e-531">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-531">Start a service</span></span>

<span data-ttu-id="e4a5e-532">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-533">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="e4a5e-534">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-534">Determine a service's status</span></span>

<span data-ttu-id="e4a5e-535">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="e4a5e-536">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="e4a5e-537">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-537">Stop a service</span></span>

<span data-ttu-id="e4a5e-538">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="e4a5e-539">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="e4a5e-539">Remove a service</span></span>

<span data-ttu-id="e4a5e-540">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="e4a5e-541">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="e4a5e-541">Handle starting and stopping events</span></span>

<span data-ttu-id="e4a5e-542">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="e4a5e-543">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="e4a5e-544">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="e4a5e-545">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="e4a5e-546">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e4a5e-547">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="e4a5e-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e4a5e-548">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="e4a5e-549">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="e4a5e-550">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="e4a5e-550">Configure endpoints</span></span>

<span data-ttu-id="e4a5e-551">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e4a5e-552">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="e4a5e-553">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="e4a5e-554">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="e4a5e-555">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="e4a5e-556">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="e4a5e-557">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-557">Current directory and content root</span></span>

<span data-ttu-id="e4a5e-558">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="e4a5e-559">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="e4a5e-560">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="e4a5e-561">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="e4a5e-562">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="e4a5e-563">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="e4a5e-564">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="e4a5e-565">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="e4a5e-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="e4a5e-566">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e4a5e-567">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="e4a5e-567">Troubleshoot</span></span>

<span data-ttu-id="e4a5e-568">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="e4a5e-569">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="e4a5e-569">Common errors</span></span>

* <span data-ttu-id="e4a5e-570">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="e4a5e-571">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="e4a5e-572">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="e4a5e-573">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="e4a5e-574">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="e4a5e-575">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="e4a5e-576">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="e4a5e-577">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="e4a5e-578">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="e4a5e-579">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="e4a5e-580">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="e4a5e-581">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="e4a5e-582">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="e4a5e-583">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a5e-583">System and Application Event Logs</span></span>

<span data-ttu-id="e4a5e-584">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="e4a5e-585">Abra o menu Iniciar, procure *Visualizador de eventos* e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e4a5e-586">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e4a5e-587">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="e4a5e-588">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e4a5e-589">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e4a5e-590">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="e4a5e-590">Run the app at a command prompt</span></span>

<span data-ttu-id="e4a5e-591">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="e4a5e-592">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="e4a5e-593">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="e4a5e-594">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="e4a5e-594">Clear package caches</span></span>

<span data-ttu-id="e4a5e-595">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="e4a5e-596">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e4a5e-597">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e4a5e-598">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e4a5e-599">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="e4a5e-600">Limpar caches de pacote também pode ser feito com a ferramenta de [nuget.exe](https://www.nuget.org/downloads) e executar o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="e4a5e-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e4a5e-601">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="e4a5e-602">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e4a5e-603">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="e4a5e-604">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="e4a5e-604">Slow or hanging app</span></span>

<span data-ttu-id="e4a5e-605">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e4a5e-606">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="e4a5e-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="e4a5e-607">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e4a5e-608">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="e4a5e-609">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e4a5e-609">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="e4a5e-610">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e4a5e-611">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e4a5e-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="e4a5e-612">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e4a5e-613">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e4a5e-614">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e4a5e-615">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="e4a5e-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e4a5e-616">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="e4a5e-617">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="e4a5e-617">Analyze the dump</span></span>

<span data-ttu-id="e4a5e-618">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="e4a5e-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e4a5e-619">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e4a5e-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4a5e-620">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e4a5e-620">Additional resources</span></span>

* <span data-ttu-id="e4a5e-621">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="e4a5e-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
