---
title: Hospedar o ASP.NET Core em um serviço Windows
author: rick-anderson
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417584"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="54b2f-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="54b2f-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54b2f-104">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="54b2f-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="54b2f-105">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="54b2f-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="54b2f-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54b2f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54b2f-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="54b2f-107">Prerequisites</span></span>

* [<span data-ttu-id="54b2f-108">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="54b2f-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="54b2f-109">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="54b2f-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="54b2f-110">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="54b2f-110">Worker Service template</span></span>

<span data-ttu-id="54b2f-111">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="54b2f-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="54b2f-112">Para usar o modelo como base para um aplicativo de Serviço Windows:</span><span class="sxs-lookup"><span data-stu-id="54b2f-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="54b2f-113">Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="54b2f-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="54b2f-114">Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.</span><span class="sxs-lookup"><span data-stu-id="54b2f-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="54b2f-115">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="54b2f-115">App configuration</span></span>

<span data-ttu-id="54b2f-116">O aplicativo requer uma referência de pacote para [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="54b2f-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="54b2f-117">`IHostBuilder.UseWindowsService`é chamado ao construir o host.</span><span class="sxs-lookup"><span data-stu-id="54b2f-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="54b2f-118">Se o aplicativo estiver sendo executado como um Serviço Windows, o método:</span><span class="sxs-lookup"><span data-stu-id="54b2f-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="54b2f-119">Define o tempo de vida do host como `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="54b2f-120">Define a raiz de [conteúdo](xref:fundamentals/index#content-root) como [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="54b2f-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="54b2f-121">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="54b2f-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="54b2f-122">Habilita o registro no registro de eventos:</span><span class="sxs-lookup"><span data-stu-id="54b2f-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="54b2f-123">O nome do aplicativo é usado como o nome de origem padrão.</span><span class="sxs-lookup"><span data-stu-id="54b2f-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="54b2f-124">O nível de registro padrão é *Aviso* ou superior para `CreateDefaultBuilder` um aplicativo com base em um modelo ASP.NET Core que chama para construir o host.</span><span class="sxs-lookup"><span data-stu-id="54b2f-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="54b2f-125">Anular o nível de `Logging:EventLog:LogLevel:Default` registro padrão com a chave em *appsettings.json*/*appsettings.{ Ambiente}.json* ou outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="54b2f-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="54b2f-126">Somente administradores podem criar novas fontes de evento.</span><span class="sxs-lookup"><span data-stu-id="54b2f-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="54b2f-127">Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.</span><span class="sxs-lookup"><span data-stu-id="54b2f-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="54b2f-128">Em `CreateHostBuilder` *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="54b2f-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="54b2f-129">Os seguintes aplicativos de exemplo acompanham este tópico:</span><span class="sxs-lookup"><span data-stu-id="54b2f-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="54b2f-130">Amostra &ndash; de serviço do trabalhador em segundo plano Uma amostra de aplicativo não web com base no [modelo de serviço](#worker-service-template) do trabalhador que usa [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="54b2f-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="54b2f-131">Amostra de &ndash; serviço do aplicativo web Uma amostra de aplicativo web razor pages que é executada como um Serviço do Windows com [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="54b2f-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="54b2f-132">Para obter orientação de <xref:mvc/overview> MVC, consulte os artigos em e <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="54b2f-133">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="54b2f-133">Deployment type</span></span>

<span data-ttu-id="54b2f-134">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="54b2f-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="54b2f-135">.</span><span class="sxs-lookup"><span data-stu-id="54b2f-135">SDK</span></span>

<span data-ttu-id="54b2f-136">Para um serviço baseado em aplicativos web que usa as páginas de barbear ou frameworks MVC, especifique o SDK da Web no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="54b2f-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="54b2f-137">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados),](xref:fundamentals/host/hosted-services)especifique o SDK do trabalhador no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="54b2f-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="54b2f-138">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="54b2f-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="54b2f-139">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="54b2f-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="54b2f-140">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="54b2f-141">Se usar o [Web SDK](#sdk), um arquivo *Web.config,* que normalmente é produzido ao publicar um aplicativo ASP.NET Core, é desnecessário para um aplicativo do Windows Services.</span><span class="sxs-lookup"><span data-stu-id="54b2f-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="54b2f-142">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="54b2f-143">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="54b2f-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="54b2f-144">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="54b2f-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="54b2f-145">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="54b2f-146">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="54b2f-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="54b2f-147">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="54b2f-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="54b2f-148">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="54b2f-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="54b2f-149">Use o [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome de propriedade RuntimeIdentifiers>(plural).</span><span class="sxs-lookup"><span data-stu-id="54b2f-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="54b2f-150">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="54b2f-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="54b2f-151">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-151">Service user account</span></span>

<span data-ttu-id="54b2f-152">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="54b2f-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="54b2f-153">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="54b2f-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-154">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="54b2f-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="54b2f-155">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="54b2f-156">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="54b2f-157">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="54b2f-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="54b2f-158">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="54b2f-159">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="54b2f-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="54b2f-160">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-160">Log on as a service rights</span></span>

<span data-ttu-id="54b2f-161">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="54b2f-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="54b2f-162">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="54b2f-163">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="54b2f-164">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="54b2f-165">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="54b2f-166">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="54b2f-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="54b2f-167">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="54b2f-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="54b2f-168">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-168">Select **Advanced**.</span></span> <span data-ttu-id="54b2f-169">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-169">Select **Find Now**.</span></span> <span data-ttu-id="54b2f-170">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="54b2f-170">Select the user account from the list.</span></span> <span data-ttu-id="54b2f-171">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-171">Select **OK**.</span></span> <span data-ttu-id="54b2f-172">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="54b2f-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="54b2f-173">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="54b2f-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="54b2f-174">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="54b2f-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="54b2f-175">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-175">Create a service</span></span>

<span data-ttu-id="54b2f-176">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="54b2f-177">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="54b2f-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="54b2f-178">`{EXE PATH}`&ndash; Caminho para a pasta do aplicativo no `d:\myservice`host (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="54b2f-179">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="54b2f-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="54b2f-180">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="54b2f-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="54b2f-181">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Conta de usuário do `Contoso\ServiceUser`serviço (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="54b2f-182">`{SERVICE NAME}`&ndash; Nome do serviço `MyService`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="54b2f-183">`{EXE FILE PATH}`&ndash; O caminho executável do aplicativo `d:\myservice\myservice.exe`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="54b2f-184">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="54b2f-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="54b2f-185">`{DESCRIPTION}`&ndash; Descrição do serviço `My sample service`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="54b2f-186">`{DISPLAY NAME}`&ndash; Nome de exibição `My Service`do serviço (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="54b2f-187">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-187">Start a service</span></span>

<span data-ttu-id="54b2f-188">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-189">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="54b2f-190">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-190">Determine a service's status</span></span>

<span data-ttu-id="54b2f-191">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-192">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="54b2f-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="54b2f-193">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-193">Stop a service</span></span>

<span data-ttu-id="54b2f-194">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="54b2f-195">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-195">Remove a service</span></span>

<span data-ttu-id="54b2f-196">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="54b2f-197">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="54b2f-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="54b2f-198">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="54b2f-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="54b2f-199">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="54b2f-200">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="54b2f-200">Configure endpoints</span></span>

<span data-ttu-id="54b2f-201">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="54b2f-202">Configure a URL e `ASPNETCORE_URLS` a porta definindo a variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="54b2f-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="54b2f-203">Para obter abordagens adicionais de configuração de URL e porta, consulte o artigo do servidor relevante:</span><span class="sxs-lookup"><span data-stu-id="54b2f-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="54b2f-204">A orientação anterior abrange o suporte para pontos finais HTTPS.</span><span class="sxs-lookup"><span data-stu-id="54b2f-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="54b2f-205">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um Serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="54b2f-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="54b2f-206">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="54b2f-207">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="54b2f-207">Current directory and content root</span></span>

<span data-ttu-id="54b2f-208">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="54b2f-209">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="54b2f-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="54b2f-210">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="54b2f-211">Usar ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="54b2f-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="54b2f-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="54b2f-213">Quando o aplicativo é <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> executado <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> como um serviço, define o [appContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="54b2f-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="54b2f-214">Os arquivos de configurações padrão do aplicativo, *appsettings.json* e *appsettings.{ O ambiente}.json*, é carregado a partir da raiz de conteúdo do aplicativo chamando [CreateDefaultBuilder durante a construção do host](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="54b2f-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="54b2f-215">Para outras configurações de arquivos <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>carregados pelo código <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>do desenvolvedor, não há necessidade de ligar .</span><span class="sxs-lookup"><span data-stu-id="54b2f-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="54b2f-216">No exemplo a seguir, o arquivo *custom_settings.json* existe na raiz de conteúdo do aplicativo e é carregado sem definir explicitamente um caminho base:</span><span class="sxs-lookup"><span data-stu-id="54b2f-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="54b2f-217">Não tente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obter um caminho de recursos porque um aplicativo do Windows Service retorna a pasta *C:\\WINDOWS\\system32* como seu diretório atual.</span><span class="sxs-lookup"><span data-stu-id="54b2f-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="54b2f-218">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="54b2f-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="54b2f-219">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="54b2f-220">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="54b2f-220">Troubleshoot</span></span>

<span data-ttu-id="54b2f-221">Para solucionar problemas de um aplicativo do Windows Service, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="54b2f-222">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="54b2f-222">Common errors</span></span>

* <span data-ttu-id="54b2f-223">Uma versão antiga ou pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="54b2f-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="54b2f-224">O serviço registrado não usa a saída **publicada** do aplicativo a partir do comando [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="54b2f-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="54b2f-225">A saída do comando [dotnet build](/dotnet/core/tools/dotnet-build) não é suportada para implantação de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="54b2f-226">Os ativos publicados são encontrados em qualquer uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="54b2f-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="54b2f-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="54b2f-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="54b2f-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="54b2f-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="54b2f-229">O serviço não está no estado de EXECUÇÃO.</span><span class="sxs-lookup"><span data-stu-id="54b2f-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="54b2f-230">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="54b2f-231">O caminho base de um Windows Service é *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="54b2f-232">O usuário não tem log on como um direito *de serviço.*</span><span class="sxs-lookup"><span data-stu-id="54b2f-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="54b2f-233">A senha do usuário é expirada ou passada `New-Service` incorretamente ao executar o comando PowerShell.</span><span class="sxs-lookup"><span data-stu-id="54b2f-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="54b2f-234">O aplicativo requer ASP.NET autenticação Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="54b2f-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="54b2f-235">A porta URL de solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="54b2f-236">Logs de eventos de sistema e aplicativos</span><span class="sxs-lookup"><span data-stu-id="54b2f-236">System and Application Event Logs</span></span>

<span data-ttu-id="54b2f-237">Acesse os registros de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="54b2f-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="54b2f-238">Abra o menu Iniciar, procure o *Visualizador de Eventos*e selecione o aplicativo **Visualizador de Eventos.**</span><span class="sxs-lookup"><span data-stu-id="54b2f-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="54b2f-239">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="54b2f-240">Selecione **Sistema** para abrir o Registro de Eventos do Sistema.</span><span class="sxs-lookup"><span data-stu-id="54b2f-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="54b2f-241">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="54b2f-242">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="54b2f-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="54b2f-243">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="54b2f-243">Run the app at a command prompt</span></span>

<span data-ttu-id="54b2f-244">Muitos erros de inicialização não produzem informações úteis nos registros de eventos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="54b2f-245">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="54b2f-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="54b2f-246">Para registrar detalhes adicionais do aplicativo, baixe o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no ambiente [Desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="54b2f-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="54b2f-247">Limpar caches de pacotes</span><span class="sxs-lookup"><span data-stu-id="54b2f-247">Clear package caches</span></span>

<span data-ttu-id="54b2f-248">Um aplicativo em funcionamento pode falhar imediatamente após atualizar o .NET Core SDK na máquina de desenvolvimento ou alterar as versões do pacote dentro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="54b2f-249">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="54b2f-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="54b2f-250">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="54b2f-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="54b2f-251">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="54b2f-252">Limpe os caches do pacote executando [os locais dotnet nuget tudo -- limpar](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="54b2f-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="54b2f-253">A limpeza de caches de pacotes também pode ser realizada `nuget locals all -clear`com a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando .</span><span class="sxs-lookup"><span data-stu-id="54b2f-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="54b2f-254">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="54b2f-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="54b2f-255">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="54b2f-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="54b2f-256">Exclua todos os arquivos da pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="54b2f-257">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="54b2f-257">Slow or hanging app</span></span>

<span data-ttu-id="54b2f-258">Um *crash dump* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="54b2f-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="54b2f-259">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="54b2f-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="54b2f-260">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="54b2f-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="54b2f-261">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="54b2f-262">Execute o [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) com o nome executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="54b2f-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="54b2f-263">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="54b2f-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="54b2f-264">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="54b2f-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="54b2f-265">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="54b2f-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="54b2f-266">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="54b2f-267">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="54b2f-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="54b2f-268">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="54b2f-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="54b2f-269">Quando um aplicativo *é travado* (pára de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [Arquivos de despejo do modo de usuário: Escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o dump.</span><span class="sxs-lookup"><span data-stu-id="54b2f-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="54b2f-270">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="54b2f-270">Analyze the dump</span></span>

<span data-ttu-id="54b2f-271">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="54b2f-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="54b2f-272">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="54b2f-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54b2f-273">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="54b2f-273">Additional resources</span></span>

* <span data-ttu-id="54b2f-274">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="54b2f-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="54b2f-275">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="54b2f-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="54b2f-276">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="54b2f-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="54b2f-277">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54b2f-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54b2f-278">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="54b2f-278">Prerequisites</span></span>

* [<span data-ttu-id="54b2f-279">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="54b2f-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="54b2f-280">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="54b2f-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="54b2f-281">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="54b2f-281">App configuration</span></span>

<span data-ttu-id="54b2f-282">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="54b2f-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="54b2f-283">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="54b2f-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="54b2f-284">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="54b2f-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="54b2f-285">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="54b2f-286">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="54b2f-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="54b2f-287">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="54b2f-288">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="54b2f-289">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="54b2f-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="54b2f-290">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="54b2f-291">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="54b2f-292">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="54b2f-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="54b2f-293">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="54b2f-294">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="54b2f-295">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="54b2f-296">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="54b2f-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="54b2f-297">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="54b2f-297">Deployment type</span></span>

<span data-ttu-id="54b2f-298">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="54b2f-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="54b2f-299">.</span><span class="sxs-lookup"><span data-stu-id="54b2f-299">SDK</span></span>

<span data-ttu-id="54b2f-300">Para um serviço baseado em aplicativos web que usa as páginas de barbear ou frameworks MVC, especifique o SDK da Web no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="54b2f-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="54b2f-301">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados),](xref:fundamentals/host/hosted-services)especifique o SDK do trabalhador no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="54b2f-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="54b2f-302">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="54b2f-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="54b2f-303">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="54b2f-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="54b2f-304">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="54b2f-305">O [Identificador de tempo de execução do Windows (RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="54b2f-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="54b2f-306">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="54b2f-307">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="54b2f-308">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="54b2f-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="54b2f-309">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="54b2f-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="54b2f-310">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="54b2f-311">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="54b2f-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="54b2f-312">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="54b2f-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="54b2f-313">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="54b2f-314">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="54b2f-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="54b2f-315">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="54b2f-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="54b2f-316">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="54b2f-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="54b2f-317">Use o [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome de propriedade RuntimeIdentifiers>(plural).</span><span class="sxs-lookup"><span data-stu-id="54b2f-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="54b2f-318">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="54b2f-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="54b2f-319">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="54b2f-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="54b2f-320">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-320">Service user account</span></span>

<span data-ttu-id="54b2f-321">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="54b2f-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="54b2f-322">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="54b2f-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-323">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="54b2f-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="54b2f-324">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="54b2f-325">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="54b2f-326">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="54b2f-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="54b2f-327">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="54b2f-328">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="54b2f-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="54b2f-329">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-329">Log on as a service rights</span></span>

<span data-ttu-id="54b2f-330">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="54b2f-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="54b2f-331">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="54b2f-332">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="54b2f-333">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="54b2f-334">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="54b2f-335">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="54b2f-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="54b2f-336">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="54b2f-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="54b2f-337">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-337">Select **Advanced**.</span></span> <span data-ttu-id="54b2f-338">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-338">Select **Find Now**.</span></span> <span data-ttu-id="54b2f-339">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="54b2f-339">Select the user account from the list.</span></span> <span data-ttu-id="54b2f-340">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-340">Select **OK**.</span></span> <span data-ttu-id="54b2f-341">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="54b2f-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="54b2f-342">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="54b2f-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="54b2f-343">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="54b2f-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="54b2f-344">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-344">Create a service</span></span>

<span data-ttu-id="54b2f-345">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="54b2f-346">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="54b2f-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="54b2f-347">`{EXE PATH}`&ndash; Caminho para a pasta do aplicativo no `d:\myservice`host (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="54b2f-348">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="54b2f-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="54b2f-349">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="54b2f-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="54b2f-350">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Conta de usuário do `Contoso\ServiceUser`serviço (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="54b2f-351">`{SERVICE NAME}`&ndash; Nome do serviço `MyService`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="54b2f-352">`{EXE FILE PATH}`&ndash; O caminho executável do aplicativo `d:\myservice\myservice.exe`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="54b2f-353">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="54b2f-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="54b2f-354">`{DESCRIPTION}`&ndash; Descrição do serviço `My sample service`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="54b2f-355">`{DISPLAY NAME}`&ndash; Nome de exibição `My Service`do serviço (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="54b2f-356">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-356">Start a service</span></span>

<span data-ttu-id="54b2f-357">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-358">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="54b2f-359">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-359">Determine a service's status</span></span>

<span data-ttu-id="54b2f-360">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-361">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="54b2f-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="54b2f-362">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-362">Stop a service</span></span>

<span data-ttu-id="54b2f-363">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="54b2f-364">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-364">Remove a service</span></span>

<span data-ttu-id="54b2f-365">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="54b2f-366">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="54b2f-366">Handle starting and stopping events</span></span>

<span data-ttu-id="54b2f-367">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="54b2f-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="54b2f-368">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="54b2f-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="54b2f-369">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="54b2f-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="54b2f-370">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="54b2f-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="54b2f-371">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="54b2f-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="54b2f-372">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="54b2f-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="54b2f-373">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="54b2f-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="54b2f-374">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="54b2f-375">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="54b2f-375">Configure endpoints</span></span>

<span data-ttu-id="54b2f-376">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="54b2f-377">Configure a URL e `ASPNETCORE_URLS` a porta definindo a variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="54b2f-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="54b2f-378">Para obter abordagens adicionais de configuração de URL e porta, consulte o artigo do servidor relevante:</span><span class="sxs-lookup"><span data-stu-id="54b2f-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="54b2f-379">A orientação anterior abrange o suporte para pontos finais HTTPS.</span><span class="sxs-lookup"><span data-stu-id="54b2f-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="54b2f-380">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um Serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="54b2f-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="54b2f-381">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="54b2f-382">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="54b2f-382">Current directory and content root</span></span>

<span data-ttu-id="54b2f-383">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="54b2f-384">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="54b2f-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="54b2f-385">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="54b2f-386">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="54b2f-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="54b2f-387">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="54b2f-388">Em vez `GetCurrentDirectory` de ligar para criar <xref:System.IO.Directory.SetCurrentDirectory*> caminhos para os arquivos de configuração, chame com o caminho para a raiz de [conteúdo](xref:fundamentals/index#content-root)do aplicativo .</span><span class="sxs-lookup"><span data-stu-id="54b2f-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="54b2f-389">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="54b2f-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="54b2f-390">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="54b2f-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="54b2f-391">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="54b2f-392">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="54b2f-392">Troubleshoot</span></span>

<span data-ttu-id="54b2f-393">Para solucionar problemas de um aplicativo do Windows Service, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="54b2f-394">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="54b2f-394">Common errors</span></span>

* <span data-ttu-id="54b2f-395">Uma versão antiga ou pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="54b2f-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="54b2f-396">O serviço registrado não usa a saída **publicada** do aplicativo a partir do comando [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="54b2f-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="54b2f-397">A saída do comando [dotnet build](/dotnet/core/tools/dotnet-build) não é suportada para implantação de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="54b2f-398">Os ativos publicados são encontrados em qualquer uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="54b2f-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="54b2f-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="54b2f-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="54b2f-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="54b2f-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="54b2f-401">O serviço não está no estado de EXECUÇÃO.</span><span class="sxs-lookup"><span data-stu-id="54b2f-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="54b2f-402">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="54b2f-403">O caminho base de um Windows Service é *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="54b2f-404">O usuário não tem log on como um direito *de serviço.*</span><span class="sxs-lookup"><span data-stu-id="54b2f-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="54b2f-405">A senha do usuário é expirada ou passada `New-Service` incorretamente ao executar o comando PowerShell.</span><span class="sxs-lookup"><span data-stu-id="54b2f-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="54b2f-406">O aplicativo requer ASP.NET autenticação Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="54b2f-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="54b2f-407">A porta URL de solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="54b2f-408">Logs de eventos de sistema e aplicativos</span><span class="sxs-lookup"><span data-stu-id="54b2f-408">System and Application Event Logs</span></span>

<span data-ttu-id="54b2f-409">Acesse os registros de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="54b2f-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="54b2f-410">Abra o menu Iniciar, procure o *Visualizador de Eventos*e selecione o aplicativo **Visualizador de Eventos.**</span><span class="sxs-lookup"><span data-stu-id="54b2f-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="54b2f-411">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="54b2f-412">Selecione **Sistema** para abrir o Registro de Eventos do Sistema.</span><span class="sxs-lookup"><span data-stu-id="54b2f-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="54b2f-413">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="54b2f-414">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="54b2f-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="54b2f-415">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="54b2f-415">Run the app at a command prompt</span></span>

<span data-ttu-id="54b2f-416">Muitos erros de inicialização não produzem informações úteis nos registros de eventos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="54b2f-417">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="54b2f-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="54b2f-418">Para registrar detalhes adicionais do aplicativo, baixe o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no ambiente [Desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="54b2f-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="54b2f-419">Limpar caches de pacotes</span><span class="sxs-lookup"><span data-stu-id="54b2f-419">Clear package caches</span></span>

<span data-ttu-id="54b2f-420">Um aplicativo em funcionamento pode falhar imediatamente após atualizar o .NET Core SDK na máquina de desenvolvimento ou alterar as versões do pacote dentro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="54b2f-421">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="54b2f-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="54b2f-422">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="54b2f-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="54b2f-423">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="54b2f-424">Limpe os caches do pacote executando [os locais dotnet nuget tudo -- limpar](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="54b2f-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="54b2f-425">A limpeza de caches de pacotes também pode ser realizada `nuget locals all -clear`com a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando .</span><span class="sxs-lookup"><span data-stu-id="54b2f-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="54b2f-426">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="54b2f-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="54b2f-427">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="54b2f-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="54b2f-428">Exclua todos os arquivos da pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="54b2f-429">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="54b2f-429">Slow or hanging app</span></span>

<span data-ttu-id="54b2f-430">Um *crash dump* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="54b2f-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="54b2f-431">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="54b2f-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="54b2f-432">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="54b2f-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="54b2f-433">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="54b2f-434">Execute o [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="54b2f-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="54b2f-435">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="54b2f-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="54b2f-436">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="54b2f-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="54b2f-437">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="54b2f-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="54b2f-438">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="54b2f-439">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="54b2f-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="54b2f-440">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="54b2f-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="54b2f-441">Quando um aplicativo *é travado* (pára de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [Arquivos de despejo do modo de usuário: Escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o dump.</span><span class="sxs-lookup"><span data-stu-id="54b2f-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="54b2f-442">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="54b2f-442">Analyze the dump</span></span>

<span data-ttu-id="54b2f-443">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="54b2f-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="54b2f-444">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="54b2f-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54b2f-445">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="54b2f-445">Additional resources</span></span>

* <span data-ttu-id="54b2f-446">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="54b2f-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="54b2f-447">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="54b2f-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="54b2f-448">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="54b2f-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="54b2f-449">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54b2f-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54b2f-450">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="54b2f-450">Prerequisites</span></span>

* [<span data-ttu-id="54b2f-451">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="54b2f-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="54b2f-452">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="54b2f-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="54b2f-453">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="54b2f-453">App configuration</span></span>

<span data-ttu-id="54b2f-454">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="54b2f-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="54b2f-455">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="54b2f-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="54b2f-456">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="54b2f-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="54b2f-457">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="54b2f-458">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="54b2f-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="54b2f-459">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="54b2f-460">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="54b2f-461">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="54b2f-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="54b2f-462">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="54b2f-463">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="54b2f-464">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="54b2f-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="54b2f-465">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="54b2f-466">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="54b2f-467">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="54b2f-468">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="54b2f-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="54b2f-469">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="54b2f-469">Deployment type</span></span>

<span data-ttu-id="54b2f-470">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="54b2f-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="54b2f-471">.</span><span class="sxs-lookup"><span data-stu-id="54b2f-471">SDK</span></span>

<span data-ttu-id="54b2f-472">Para um serviço baseado em aplicativos web que usa as páginas de barbear ou frameworks MVC, especifique o SDK da Web no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="54b2f-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="54b2f-473">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados),](xref:fundamentals/host/hosted-services)especifique o SDK do trabalhador no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="54b2f-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="54b2f-474">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="54b2f-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="54b2f-475">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="54b2f-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="54b2f-476">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="54b2f-477">O [Identificador de tempo de execução do Windows (RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="54b2f-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="54b2f-478">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="54b2f-479">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="54b2f-480">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="54b2f-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="54b2f-481">A propriedade `<UseAppHost>` é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="54b2f-482">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="54b2f-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="54b2f-483">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="54b2f-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="54b2f-484">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="54b2f-485">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="54b2f-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="54b2f-486">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="54b2f-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="54b2f-487">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="54b2f-488">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="54b2f-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="54b2f-489">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="54b2f-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="54b2f-490">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="54b2f-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="54b2f-491">Use o [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome de propriedade RuntimeIdentifiers>(plural).</span><span class="sxs-lookup"><span data-stu-id="54b2f-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="54b2f-492">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="54b2f-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="54b2f-493">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="54b2f-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="54b2f-494">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-494">Service user account</span></span>

<span data-ttu-id="54b2f-495">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="54b2f-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="54b2f-496">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="54b2f-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-497">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="54b2f-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="54b2f-498">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="54b2f-499">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="54b2f-500">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="54b2f-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="54b2f-501">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="54b2f-502">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="54b2f-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="54b2f-503">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-503">Log on as a service rights</span></span>

<span data-ttu-id="54b2f-504">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="54b2f-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="54b2f-505">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="54b2f-506">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="54b2f-507">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="54b2f-508">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="54b2f-509">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="54b2f-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="54b2f-510">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="54b2f-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="54b2f-511">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-511">Select **Advanced**.</span></span> <span data-ttu-id="54b2f-512">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-512">Select **Find Now**.</span></span> <span data-ttu-id="54b2f-513">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="54b2f-513">Select the user account from the list.</span></span> <span data-ttu-id="54b2f-514">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-514">Select **OK**.</span></span> <span data-ttu-id="54b2f-515">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="54b2f-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="54b2f-516">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="54b2f-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="54b2f-517">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="54b2f-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="54b2f-518">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-518">Create a service</span></span>

<span data-ttu-id="54b2f-519">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="54b2f-520">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="54b2f-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="54b2f-521">`{EXE PATH}`&ndash; Caminho para a pasta do aplicativo no `d:\myservice`host (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="54b2f-522">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="54b2f-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="54b2f-523">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="54b2f-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="54b2f-524">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Conta de usuário do `Contoso\ServiceUser`serviço (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="54b2f-525">`{SERVICE NAME}`&ndash; Nome do serviço `MyService`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="54b2f-526">`{EXE FILE PATH}`&ndash; O caminho executável do aplicativo `d:\myservice\myservice.exe`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="54b2f-527">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="54b2f-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="54b2f-528">`{DESCRIPTION}`&ndash; Descrição do serviço `My sample service`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="54b2f-529">`{DISPLAY NAME}`&ndash; Nome de exibição `My Service`do serviço (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="54b2f-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="54b2f-530">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-530">Start a service</span></span>

<span data-ttu-id="54b2f-531">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-532">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="54b2f-533">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-533">Determine a service's status</span></span>

<span data-ttu-id="54b2f-534">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="54b2f-535">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="54b2f-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="54b2f-536">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-536">Stop a service</span></span>

<span data-ttu-id="54b2f-537">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="54b2f-538">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="54b2f-538">Remove a service</span></span>

<span data-ttu-id="54b2f-539">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="54b2f-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="54b2f-540">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="54b2f-540">Handle starting and stopping events</span></span>

<span data-ttu-id="54b2f-541">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="54b2f-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="54b2f-542">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="54b2f-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="54b2f-543">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="54b2f-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="54b2f-544">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="54b2f-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="54b2f-545">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="54b2f-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="54b2f-546">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="54b2f-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="54b2f-547">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="54b2f-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="54b2f-548">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="54b2f-549">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="54b2f-549">Configure endpoints</span></span>

<span data-ttu-id="54b2f-550">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="54b2f-551">Configure a URL e `ASPNETCORE_URLS` a porta definindo a variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="54b2f-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="54b2f-552">Para obter abordagens adicionais de configuração de URL e porta, consulte o artigo do servidor relevante:</span><span class="sxs-lookup"><span data-stu-id="54b2f-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="54b2f-553">A orientação anterior abrange o suporte para pontos finais HTTPS.</span><span class="sxs-lookup"><span data-stu-id="54b2f-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="54b2f-554">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um Serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="54b2f-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="54b2f-555">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="54b2f-556">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="54b2f-556">Current directory and content root</span></span>

<span data-ttu-id="54b2f-557">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="54b2f-558">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="54b2f-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="54b2f-559">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="54b2f-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="54b2f-560">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="54b2f-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="54b2f-561">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="54b2f-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="54b2f-562">Em vez `GetCurrentDirectory` de ligar para criar <xref:System.IO.Directory.SetCurrentDirectory*> caminhos para os arquivos de configuração, chame com o caminho para a raiz de [conteúdo](xref:fundamentals/index#content-root)do aplicativo .</span><span class="sxs-lookup"><span data-stu-id="54b2f-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="54b2f-563">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="54b2f-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="54b2f-564">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="54b2f-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="54b2f-565">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="54b2f-566">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="54b2f-566">Troubleshoot</span></span>

<span data-ttu-id="54b2f-567">Para solucionar problemas de um aplicativo do Windows Service, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="54b2f-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="54b2f-568">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="54b2f-568">Common errors</span></span>

* <span data-ttu-id="54b2f-569">Uma versão antiga ou pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="54b2f-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="54b2f-570">O serviço registrado não usa a saída **publicada** do aplicativo a partir do comando [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="54b2f-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="54b2f-571">A saída do comando [dotnet build](/dotnet/core/tools/dotnet-build) não é suportada para implantação de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="54b2f-572">Os ativos publicados são encontrados em qualquer uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="54b2f-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="54b2f-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="54b2f-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="54b2f-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="54b2f-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="54b2f-575">O serviço não está no estado de EXECUÇÃO.</span><span class="sxs-lookup"><span data-stu-id="54b2f-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="54b2f-576">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="54b2f-577">O caminho base de um Windows Service é *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="54b2f-578">O usuário não tem log on como um direito *de serviço.*</span><span class="sxs-lookup"><span data-stu-id="54b2f-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="54b2f-579">A senha do usuário é expirada ou passada `New-Service` incorretamente ao executar o comando PowerShell.</span><span class="sxs-lookup"><span data-stu-id="54b2f-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="54b2f-580">O aplicativo requer ASP.NET autenticação Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="54b2f-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="54b2f-581">A porta URL de solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="54b2f-582">Logs de eventos de sistema e aplicativos</span><span class="sxs-lookup"><span data-stu-id="54b2f-582">System and Application Event Logs</span></span>

<span data-ttu-id="54b2f-583">Acesse os registros de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="54b2f-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="54b2f-584">Abra o menu Iniciar, procure o *Visualizador de Eventos*e selecione o aplicativo **Visualizador de Eventos.**</span><span class="sxs-lookup"><span data-stu-id="54b2f-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="54b2f-585">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="54b2f-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="54b2f-586">Selecione **Sistema** para abrir o Registro de Eventos do Sistema.</span><span class="sxs-lookup"><span data-stu-id="54b2f-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="54b2f-587">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="54b2f-588">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="54b2f-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="54b2f-589">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="54b2f-589">Run the app at a command prompt</span></span>

<span data-ttu-id="54b2f-590">Muitos erros de inicialização não produzem informações úteis nos registros de eventos.</span><span class="sxs-lookup"><span data-stu-id="54b2f-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="54b2f-591">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="54b2f-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="54b2f-592">Para registrar detalhes adicionais do aplicativo, baixe o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no ambiente [Desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="54b2f-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="54b2f-593">Limpar caches de pacotes</span><span class="sxs-lookup"><span data-stu-id="54b2f-593">Clear package caches</span></span>

<span data-ttu-id="54b2f-594">Um aplicativo em funcionamento pode falhar imediatamente após atualizar o .NET Core SDK na máquina de desenvolvimento ou alterar as versões do pacote dentro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="54b2f-595">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="54b2f-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="54b2f-596">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="54b2f-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="54b2f-597">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="54b2f-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="54b2f-598">Limpe os caches do pacote executando [os locais dotnet nuget tudo -- limpar](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="54b2f-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="54b2f-599">A limpeza de caches de pacotes também pode ser realizada `nuget locals all -clear`com a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando .</span><span class="sxs-lookup"><span data-stu-id="54b2f-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="54b2f-600">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="54b2f-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="54b2f-601">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="54b2f-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="54b2f-602">Exclua todos os arquivos da pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="54b2f-603">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="54b2f-603">Slow or hanging app</span></span>

<span data-ttu-id="54b2f-604">Um *crash dump* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="54b2f-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="54b2f-605">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="54b2f-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="54b2f-606">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="54b2f-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="54b2f-607">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="54b2f-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="54b2f-608">Execute o [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="54b2f-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="54b2f-609">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="54b2f-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="54b2f-610">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="54b2f-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="54b2f-611">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="54b2f-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="54b2f-612">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b2f-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="54b2f-613">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="54b2f-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="54b2f-614">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="54b2f-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="54b2f-615">Quando um aplicativo *é travado* (pára de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [Arquivos de despejo do modo de usuário: Escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o dump.</span><span class="sxs-lookup"><span data-stu-id="54b2f-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="54b2f-616">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="54b2f-616">Analyze the dump</span></span>

<span data-ttu-id="54b2f-617">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="54b2f-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="54b2f-618">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="54b2f-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54b2f-619">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="54b2f-619">Additional resources</span></span>

* <span data-ttu-id="54b2f-620">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="54b2f-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
