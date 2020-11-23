---
title: Azure Key Vault provedor de configuração no ASP.NET Core
author: rick-anderson
description: Saiba como usar o provedor de configuração Azure Key Vault para configurar um aplicativo usando pares de nome-valor carregados em tempo de execução.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
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
uid: security/key-vault-configuration
ms.openlocfilehash: fcd5524bed11cca2380ffd8956f437f742729b55
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417599"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="e1a6e-103">Azure Key Vault provedor de configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1a6e-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="e1a6e-104">Por [Andrew Stanton-enfermaria](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e1a6e-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1a6e-105">Este documento explica como usar o provedor de configuração [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="e1a6e-106">Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="e1a6e-107">Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="e1a6e-108">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="e1a6e-109">Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="e1a6e-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1a6e-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="e1a6e-111">Pacotes</span><span class="sxs-lookup"><span data-stu-id="e1a6e-111">Packages</span></span>

<span data-ttu-id="e1a6e-112">Adicione uma referência de pacote ao [Azure.Extensions.AspNetCore.Configuração. ](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) Pacote de segredos.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-112">Add a package reference to the [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="e1a6e-113">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="e1a6e-113">Sample app</span></span>

<span data-ttu-id="e1a6e-114">O aplicativo de exemplo é executado em um dos dois modos determinados pela `#define` instrução na parte superior do arquivo *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="e1a6e-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="e1a6e-115">`Certificate`: Demonstra o uso de uma Azure Key Vault ID do cliente e do certificado X. 509 para acessar os segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="e1a6e-116">Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="e1a6e-117">`Managed`: Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="e1a6e-118">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="e1a6e-119">A `Managed` versão do exemplo deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="e1a6e-120">Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="e1a6e-121">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador ( `#define` ), consulte <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="e1a6e-122">Armazenamento de segredo no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="e1a6e-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="e1a6e-123">Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="e1a6e-124">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="e1a6e-125">A ferramenta Gerenciador de segredo requer uma `<UserSecretsId>` propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="e1a6e-126">Defina o valor da propriedade ( `{GUID}` ) para qualquer Guid exclusivo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="e1a6e-127">Os segredos são criados como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="e1a6e-128">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="e1a6e-129">O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="e1a6e-130">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="e1a6e-131">Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para `_prod` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="e1a6e-132">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="e1a6e-133">Armazenamento de segredo no ambiente de produção com Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e1a6e-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="e1a6e-134">As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="e1a6e-135">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="e1a6e-136">Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="e1a6e-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="e1a6e-137">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="e1a6e-138">Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="e1a6e-139">Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="e1a6e-140">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="e1a6e-141">Para obter mais informações, consulte [CLI do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="e1a6e-142">Se você ainda não estiver autenticado, entre com o `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="e1a6e-143">Crie um grupo de recursos com o seguinte comando, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="e1a6e-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="e1a6e-144">Crie um cofre de chaves no grupo de recursos com o seguinte comando, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="e1a6e-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="e1a6e-145">Crie segredos no cofre de chaves como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="e1a6e-146">Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="e1a6e-147">Os valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="e1a6e-148">Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="e1a6e-149">Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="e1a6e-150">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="e1a6e-151">Os valores incluem um `_prod` sufixo para distingui-los dos `_dev` valores de sufixo carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="e1a6e-152">Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="e1a6e-153">Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="e1a6e-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="e1a6e-154">Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="e1a6e-155">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="e1a6e-156">Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="e1a6e-157">Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="e1a6e-158">O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="e1a6e-159">Crie um certificado PKCS # 12 arquivo morto (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="e1a6e-160">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="e1a6e-161">Instale o certificado no repositório de certificados pessoal do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="e1a6e-162">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="e1a6e-163">Observe a impressão digital do certificado, que é usada posteriormente neste processo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="e1a6e-164">Exporte o certificado PKCS # 12 Archive (*. pfx*) como um certificado codificado em der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="e1a6e-165">Registre o aplicativo com o Azure AD (**registros de aplicativo**).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="e1a6e-166">Carregue o certificado codificado em DER (*. cer*) no Azure AD:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="e1a6e-167">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="e1a6e-168">Navegue até **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="e1a6e-169">Selecione **carregar certificado** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="e1a6e-170">Um certificado *. cer*, *. pem* ou *. CRT* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="e1a6e-171">Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado no arquivo do aplicativo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="e1a6e-172">Navegue até **cofres de chaves** na portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="e1a6e-173">Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="e1a6e-174">Selecione **Políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="e1a6e-175">Selecione **Adicionar Política de Acesso**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="e1a6e-176">Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="e1a6e-177">Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="e1a6e-178">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="e1a6e-179">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-179">Select **OK**.</span></span>
1. <span data-ttu-id="e1a6e-180">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-180">Select **Save**.</span></span>
1. <span data-ttu-id="e1a6e-181">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-181">Deploy the app.</span></span>

<span data-ttu-id="e1a6e-182">O `Certificate` aplicativo de exemplo obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="e1a6e-183">Valores não hierárquicos: o valor para `SecretName` é obtido com `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="e1a6e-184">Valores hierárquicos (seções): `:` notação de uso (dois-pontos) ou o `GetSection` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="e1a6e-185">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="e1a6e-186">O certificado X. 509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="e1a6e-187">O aplicativo chama **AddAzureKeyVault** com valores fornecidos pelo *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-187">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="e1a6e-188">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-188">Example values:</span></span>

* <span data-ttu-id="e1a6e-189">Nome do cofre de chaves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="e1a6e-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="e1a6e-190">ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="e1a6e-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="e1a6e-191">Impressão digital do certificado: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="e1a6e-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="e1a6e-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="e1a6e-193">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="e1a6e-194">No ambiente de desenvolvimento, os valores secretos são carregados com o `_dev` sufixo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="e1a6e-195">No ambiente de produção, os valores são carregados com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="e1a6e-196">Usar identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="e1a6e-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="e1a6e-197">**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="e1a6e-198">O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Managed` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="e1a6e-199">Insira o nome do cofre no arquivo do aplicativo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="e1a6e-200">O aplicativo de exemplo não requer uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a `Managed` versão, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="e1a6e-201">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="e1a6e-202">Implante o aplicativo de exemplo no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="e1a6e-203">Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="e1a6e-204">Obtenha a ID de objeto da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="e1a6e-205">A ID de objeto é mostrada no portal do Azure no **Identity** painel do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="e1a6e-206">Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo `list` e `get` permissões para acessar o cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="e1a6e-207">**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="e1a6e-208">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-208">The sample app:</span></span>

* <span data-ttu-id="e1a6e-209">Cria uma instância da `DefaultAzureCredential` classe, a credencial tenta obter um token de acesso do ambiente para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-209">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="e1a6e-210">Um novo [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) é criado com a `DefaultAzureCredential` instância do.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-210">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="e1a6e-211">A `Azure.Security.KeyVault.Secrets.Secrets` instância é usada com uma implementação padrão do `Azure.Extensions.Aspnetcore.Configuration.Secrets` que carrega todos os valores secretos e substitui traços duplos ( `--` ) por dois-pontos ( `:` ) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-211">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="e1a6e-212">Valor de exemplo do nome do cofre de chaves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="e1a6e-212">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="e1a6e-213">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-213">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="e1a6e-214">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="e1a6e-215">No ambiente de desenvolvimento, os valores secretos têm o `_dev` sufixo porque são fornecidos por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="e1a6e-216">No ambiente de produção, os valores são carregados com o `_prod` sufixo porque são fornecidos por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="e1a6e-217">Se você receber um `Access denied` erro, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="e1a6e-218">Confirme que você reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-218">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="e1a6e-219">Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-219">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="e1a6e-220">Opções de configuração</span><span class="sxs-lookup"><span data-stu-id="e1a6e-220">Configuration options</span></span>

<span data-ttu-id="e1a6e-221">AddAzureKeyVault pode aceitar um AzureKeyVaultConfigurationOptions:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-221">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="e1a6e-222">Propriedade</span><span class="sxs-lookup"><span data-stu-id="e1a6e-222">Property</span></span>         | <span data-ttu-id="e1a6e-223">Descrição</span><span class="sxs-lookup"><span data-stu-id="e1a6e-223">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="e1a6e-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instância usada para controlar o carregamento de segredo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="e1a6e-225">`Timespan` aguardar entre as tentativas de sondagem do cofre de chaves para alterações.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-225">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="e1a6e-226">O valor padrão é `null` (a configuração não é recarregada).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-226">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="e1a6e-227">Usar um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="e1a6e-227">Use a key name prefix</span></span>

<span data-ttu-id="e1a6e-228">O AddAzureKeyVault fornece uma sobrecarga que aceita uma implementação do `Azure.Extensions.Aspnetcore.Configuration.Secrets` , que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-228">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="e1a6e-229">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-229">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="e1a6e-230">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-230">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="e1a6e-231">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-231">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="e1a6e-232">Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-232">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="e1a6e-233">No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-233">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="e1a6e-234">Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-234">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="e1a6e-235">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para o `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-235">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="e1a6e-236">Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret` , eliminando a versão à medida que ela carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-236">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="e1a6e-237">AddAzureKeyVault é chamado com um personalizado `Azure.Extensions.Aspnetcore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="e1a6e-237">AddAzureKeyVault is called with a custom `Azure.Extensions.Aspnetcore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="e1a6e-238">A `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementação reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-238">The `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="e1a6e-239">`Load` carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-239">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="e1a6e-240">Outros segredos não são carregados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-240">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="e1a6e-241">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-241">`GetKey`:</span></span>
  * <span data-ttu-id="e1a6e-242">Remove o prefixo do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-242">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="e1a6e-243">Substitui dois traços em qualquer nome pelo `KeyDelimiter` , que é o delimitador usado na configuração (geralmente um dois-pontos).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-243">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="e1a6e-244">Azure Key Vault não permite dois pontos em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-244">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="e1a6e-245">O `Load` método é chamado por um algoritmo de provedor que itera por meio dos segredos do cofre para localizar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-245">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="e1a6e-246">Quando um prefixo de versão é encontrado com `Load` , o algoritmo usa o `GetKey` método para retornar o nome de configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-246">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="e1a6e-247">Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-247">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="e1a6e-248">Quando essa abordagem é implementada:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-248">When this approach is implemented:</span></span>

1. <span data-ttu-id="e1a6e-249">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-249">The app's version specified in the app's project file.</span></span> <span data-ttu-id="e1a6e-250">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="e1a6e-250">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e1a6e-251">Confirme se uma `<UserSecretsId>` propriedade está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-251">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="e1a6e-252">Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="e1a6e-252">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="e1a6e-253">Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-253">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="e1a6e-254">Quando o aplicativo é executado, os segredos do cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-254">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="e1a6e-255">O segredo da cadeia de caracteres para `5000-AppSecret` é correspondido à versão do aplicativo especificada no arquivo de projeto do aplicativo ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-255">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="e1a6e-256">A versão, `5000` (com o Dash), é removida do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-256">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="e1a6e-257">Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-257">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="e1a6e-258">Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado estará `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-258">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="e1a6e-259">Você também pode fornecer sua própria <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementação para AddAzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-259">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="e1a6e-260">Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-260">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="e1a6e-261">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="e1a6e-261">Bind an array to a class</span></span>

<span data-ttu-id="e1a6e-262">O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-262">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="e1a6e-263">Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois-pontos ( `:` ), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-263">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="e1a6e-264">Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-264">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="e1a6e-265">Azure Key Vault chaves não podem usar dois-pontos como separador.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-265">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="e1a6e-266">A abordagem descrita neste tópico usa traços duplos ( `--` ) como separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-266">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="e1a6e-267">As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-267">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="e1a6e-268">Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-268">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="e1a6e-269">Há dois literais de objeto definidos na `WriteTo` matriz que refletem dois *coletores* Serilog, que descrevem os destinos para a saída de log:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-269">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="e1a6e-270">A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a `--` notação de traço duplo () e os segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-270">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="e1a6e-271">Chave</span><span class="sxs-lookup"><span data-stu-id="e1a6e-271">Key</span></span> | <span data-ttu-id="e1a6e-272">Valor</span><span class="sxs-lookup"><span data-stu-id="e1a6e-272">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="e1a6e-273">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="e1a6e-273">Reload secrets</span></span>

<span data-ttu-id="e1a6e-274">Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-274">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="e1a6e-275">Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que o `Reload` seja executado.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-275">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="e1a6e-276">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="e1a6e-276">Disabled and expired secrets</span></span>

<span data-ttu-id="e1a6e-277">Os segredos desabilitados e expirados lançam um <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-277">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="e1a6e-278">Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-278">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e1a6e-279">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="e1a6e-279">Troubleshoot</span></span>

<span data-ttu-id="e1a6e-280">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-280">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e1a6e-281">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-281">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="e1a6e-282">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-282">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="e1a6e-283">O cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-283">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="e1a6e-284">O aplicativo não está autorizado a acessar o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-284">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="e1a6e-285">A política de acesso não `Get` inclui `List` permissões e.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-285">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="e1a6e-286">No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-286">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="e1a6e-287">O aplicativo tem o nome do cofre de chaves errado ( `KeyVaultName` ), a ID do aplicativo do Azure AD ( `AzureADApplicationId` ) ou a impressão digital do certificado do Azure AD ( `AzureADCertThumbprint` ) ou o directoryid () do Azure ad `AzureADDirectoryId` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-287">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="e1a6e-288">A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-288">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="e1a6e-289">Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-289">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1a6e-290">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e1a6e-290">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="e1a6e-291">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="e1a6e-291">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="e1a6e-292">Microsoft Azure: Key Vault documentação</span><span class="sxs-lookup"><span data-stu-id="e1a6e-292">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="e1a6e-293">Como gerar e transferir chaves protegidas por HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e1a6e-293">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="e1a6e-294">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="e1a6e-294">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="e1a6e-295">Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET</span><span class="sxs-lookup"><span data-stu-id="e1a6e-295">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="e1a6e-296">Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET</span><span class="sxs-lookup"><span data-stu-id="e1a6e-296">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1a6e-297">Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-297">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="e1a6e-298">Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-298">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="e1a6e-299">Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-299">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="e1a6e-300">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-300">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="e1a6e-301">Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-301">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="e1a6e-302">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1a6e-302">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="e1a6e-303">Pacotes</span><span class="sxs-lookup"><span data-stu-id="e1a6e-303">Packages</span></span>

<span data-ttu-id="e1a6e-304">Adicione uma referência de pacote ao [Microsoft.Extensions.Configuração. Pacote AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-304">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="e1a6e-305">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="e1a6e-305">Sample app</span></span>

<span data-ttu-id="e1a6e-306">O aplicativo de exemplo é executado em um dos dois modos determinados pela `#define` instrução na parte superior do arquivo *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="e1a6e-306">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="e1a6e-307">`Certificate`: Demonstra o uso de uma Azure Key Vault ID do cliente e do certificado X. 509 para acessar os segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-307">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="e1a6e-308">Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-308">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="e1a6e-309">`Managed`: Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-309">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="e1a6e-310">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-310">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="e1a6e-311">A `Managed` versão do exemplo deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-311">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="e1a6e-312">Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-312">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="e1a6e-313">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador ( `#define` ), consulte <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-313">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="e1a6e-314">Armazenamento de segredo no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="e1a6e-314">Secret storage in the Development environment</span></span>

<span data-ttu-id="e1a6e-315">Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-315">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="e1a6e-316">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-316">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="e1a6e-317">A ferramenta Gerenciador de segredo requer uma `<UserSecretsId>` propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-317">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="e1a6e-318">Defina o valor da propriedade ( `{GUID}` ) para qualquer Guid exclusivo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-318">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="e1a6e-319">Os segredos são criados como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-319">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="e1a6e-320">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-320">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="e1a6e-321">O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-321">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="e1a6e-322">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-322">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="e1a6e-323">Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para `_prod` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-323">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="e1a6e-324">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-324">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="e1a6e-325">Armazenamento de segredo no ambiente de produção com Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e1a6e-325">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="e1a6e-326">As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-326">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="e1a6e-327">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-327">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="e1a6e-328">Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="e1a6e-328">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="e1a6e-329">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-329">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="e1a6e-330">Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-330">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="e1a6e-331">Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-331">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="e1a6e-332">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-332">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="e1a6e-333">Para obter mais informações, consulte [CLI do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-333">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="e1a6e-334">Se você ainda não estiver autenticado, entre com o `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-334">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="e1a6e-335">Crie um grupo de recursos com o seguinte comando, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="e1a6e-335">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="e1a6e-336">Crie um cofre de chaves no grupo de recursos com o seguinte comando, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="e1a6e-336">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="e1a6e-337">Crie segredos no cofre de chaves como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-337">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="e1a6e-338">Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-338">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="e1a6e-339">Os valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-339">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="e1a6e-340">Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-340">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="e1a6e-341">Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-341">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="e1a6e-342">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-342">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="e1a6e-343">Os valores incluem um `_prod` sufixo para distingui-los dos `_dev` valores de sufixo carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-343">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="e1a6e-344">Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-344">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="e1a6e-345">Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="e1a6e-345">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="e1a6e-346">Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-346">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="e1a6e-347">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-347">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="e1a6e-348">Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-348">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="e1a6e-349">Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-349">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="e1a6e-350">O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-350">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="e1a6e-351">Crie um certificado PKCS # 12 arquivo morto (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-351">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="e1a6e-352">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-352">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="e1a6e-353">Instale o certificado no repositório de certificados pessoal do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-353">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="e1a6e-354">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-354">Marking the key as exportable is optional.</span></span> <span data-ttu-id="e1a6e-355">Observe a impressão digital do certificado, que é usada posteriormente neste processo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-355">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="e1a6e-356">Exporte o certificado PKCS # 12 Archive (*. pfx*) como um certificado codificado em der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-356">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="e1a6e-357">Registre o aplicativo com o Azure AD (**registros de aplicativo**).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-357">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="e1a6e-358">Carregue o certificado codificado em DER (*. cer*) no Azure AD:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-358">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="e1a6e-359">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-359">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="e1a6e-360">Navegue até **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-360">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="e1a6e-361">Selecione **carregar certificado** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-361">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="e1a6e-362">Um certificado *. cer*, *. pem* ou *. CRT* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-362">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="e1a6e-363">Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado no arquivo do aplicativo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-363">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="e1a6e-364">Navegue até **cofres de chaves** na portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-364">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="e1a6e-365">Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-365">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="e1a6e-366">Selecione **Políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-366">Select **Access policies**.</span></span>
1. <span data-ttu-id="e1a6e-367">Selecione **Adicionar Política de Acesso**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-367">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="e1a6e-368">Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-368">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="e1a6e-369">Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-369">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="e1a6e-370">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-370">Select the **Select** button.</span></span>
1. <span data-ttu-id="e1a6e-371">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-371">Select **OK**.</span></span>
1. <span data-ttu-id="e1a6e-372">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-372">Select **Save**.</span></span>
1. <span data-ttu-id="e1a6e-373">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-373">Deploy the app.</span></span>

<span data-ttu-id="e1a6e-374">O `Certificate` aplicativo de exemplo obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-374">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="e1a6e-375">Valores não hierárquicos: o valor para `SecretName` é obtido com `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-375">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="e1a6e-376">Valores hierárquicos (seções): `:` notação de uso (dois-pontos) ou o `GetSection` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-376">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="e1a6e-377">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-377">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="e1a6e-378">O certificado X. 509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-378">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="e1a6e-379">O aplicativo chama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> com valores fornecidos pelo *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-379">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="e1a6e-380">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-380">Example values:</span></span>

* <span data-ttu-id="e1a6e-381">Nome do cofre de chaves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="e1a6e-381">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="e1a6e-382">ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="e1a6e-382">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="e1a6e-383">Impressão digital do certificado: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="e1a6e-383">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="e1a6e-384">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-384">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="e1a6e-385">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-385">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="e1a6e-386">No ambiente de desenvolvimento, os valores secretos são carregados com o `_dev` sufixo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-386">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="e1a6e-387">No ambiente de produção, os valores são carregados com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-387">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="e1a6e-388">Usar identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="e1a6e-388">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="e1a6e-389">**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-389">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="e1a6e-390">O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Managed` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-390">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="e1a6e-391">Insira o nome do cofre no arquivo do aplicativo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-391">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="e1a6e-392">O aplicativo de exemplo não requer uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a `Managed` versão, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-392">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="e1a6e-393">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-393">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="e1a6e-394">Implante o aplicativo de exemplo no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-394">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="e1a6e-395">Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-395">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="e1a6e-396">Obtenha a ID de objeto da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-396">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="e1a6e-397">A ID de objeto é mostrada no portal do Azure no **Identity** painel do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-397">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="e1a6e-398">Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo `list` e `get` permissões para acessar o cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-398">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="e1a6e-399">**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-399">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="e1a6e-400">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-400">The sample app:</span></span>

* <span data-ttu-id="e1a6e-401">Cria uma instância da `AzureServiceTokenProvider` classe sem uma cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-401">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="e1a6e-402">Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-402">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="e1a6e-403">Um novo <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é criado com o `AzureServiceTokenProvider` retorno de chamada de token de instância.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-403">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="e1a6e-404">A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com uma implementação padrão do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carrega todos os valores secretos e substitui traços duplos ( `--` ) por dois-pontos ( `:` ) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-404">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="e1a6e-405">Valor de exemplo do nome do cofre de chaves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="e1a6e-405">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="e1a6e-406">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-406">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="e1a6e-407">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-407">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="e1a6e-408">No ambiente de desenvolvimento, os valores secretos têm o `_dev` sufixo porque são fornecidos por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-408">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="e1a6e-409">No ambiente de produção, os valores são carregados com o `_prod` sufixo porque são fornecidos por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-409">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="e1a6e-410">Se você receber um `Access denied` erro, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-410">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="e1a6e-411">Confirme que você reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-411">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="e1a6e-412">Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-412">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="e1a6e-413">Usar um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="e1a6e-413">Use a key name prefix</span></span>

<span data-ttu-id="e1a6e-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fornece uma sobrecarga que aceita uma implementação do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="e1a6e-415">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-415">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="e1a6e-416">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-416">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="e1a6e-417">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-417">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="e1a6e-418">Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-418">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="e1a6e-419">No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-419">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="e1a6e-420">Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-420">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="e1a6e-421">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para o `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-421">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="e1a6e-422">Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret` , eliminando a versão à medida que ela carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-422">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="e1a6e-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> é chamado com um personalizado <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="e1a6e-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="e1a6e-424">A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-424">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="e1a6e-425">`Load` carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-425">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="e1a6e-426">Outros segredos não são carregados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-426">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="e1a6e-427">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-427">`GetKey`:</span></span>
  * <span data-ttu-id="e1a6e-428">Remove o prefixo do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-428">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="e1a6e-429">Substitui dois traços em qualquer nome pelo `KeyDelimiter` , que é o delimitador usado na configuração (geralmente um dois-pontos).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-429">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="e1a6e-430">Azure Key Vault não permite dois pontos em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-430">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="e1a6e-431">O `Load` método é chamado por um algoritmo de provedor que itera por meio dos segredos do cofre para localizar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-431">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="e1a6e-432">Quando um prefixo de versão é encontrado com `Load` , o algoritmo usa o `GetKey` método para retornar o nome de configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-432">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="e1a6e-433">Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-433">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="e1a6e-434">Quando essa abordagem é implementada:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-434">When this approach is implemented:</span></span>

1. <span data-ttu-id="e1a6e-435">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-435">The app's version specified in the app's project file.</span></span> <span data-ttu-id="e1a6e-436">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="e1a6e-436">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e1a6e-437">Confirme se uma `<UserSecretsId>` propriedade está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-437">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="e1a6e-438">Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="e1a6e-438">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="e1a6e-439">Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-439">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="e1a6e-440">Quando o aplicativo é executado, os segredos do cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-440">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="e1a6e-441">O segredo da cadeia de caracteres para `5000-AppSecret` é correspondido à versão do aplicativo especificada no arquivo de projeto do aplicativo ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-441">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="e1a6e-442">A versão, `5000` (com o Dash), é removida do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-442">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="e1a6e-443">Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-443">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="e1a6e-444">Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado estará `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-444">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="e1a6e-445">Você também pode fornecer sua própria <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementação para o <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-445">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="e1a6e-446">Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-446">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="e1a6e-447">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="e1a6e-447">Bind an array to a class</span></span>

<span data-ttu-id="e1a6e-448">O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-448">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="e1a6e-449">Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois-pontos ( `:` ), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-449">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="e1a6e-450">Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-450">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="e1a6e-451">Azure Key Vault chaves não podem usar dois-pontos como separador.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-451">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="e1a6e-452">A abordagem descrita neste tópico usa traços duplos ( `--` ) como separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-452">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="e1a6e-453">As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-453">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="e1a6e-454">Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-454">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="e1a6e-455">Há dois literais de objeto definidos na `WriteTo` matriz que refletem dois *coletores* Serilog, que descrevem os destinos para a saída de log:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-455">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="e1a6e-456">A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a `--` notação de traço duplo () e os segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-456">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="e1a6e-457">Chave</span><span class="sxs-lookup"><span data-stu-id="e1a6e-457">Key</span></span> | <span data-ttu-id="e1a6e-458">Valor</span><span class="sxs-lookup"><span data-stu-id="e1a6e-458">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="e1a6e-459">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="e1a6e-459">Reload secrets</span></span>

<span data-ttu-id="e1a6e-460">Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-460">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="e1a6e-461">Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que o `Reload` seja executado.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-461">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="e1a6e-462">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="e1a6e-462">Disabled and expired secrets</span></span>

<span data-ttu-id="e1a6e-463">Os segredos desabilitados e expirados lançam um <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-463">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="e1a6e-464">Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-464">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e1a6e-465">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="e1a6e-465">Troubleshoot</span></span>

<span data-ttu-id="e1a6e-466">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-466">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e1a6e-467">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="e1a6e-467">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="e1a6e-468">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-468">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="e1a6e-469">O cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-469">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="e1a6e-470">O aplicativo não está autorizado a acessar o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-470">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="e1a6e-471">A política de acesso não `Get` inclui `List` permissões e.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-471">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="e1a6e-472">No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-472">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="e1a6e-473">O aplicativo tem o nome do cofre de chaves errado ( `KeyVaultName` ), a ID do aplicativo do Azure AD ( `AzureADApplicationId` ) ou a impressão digital do certificado do Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="e1a6e-473">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="e1a6e-474">A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="e1a6e-474">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="e1a6e-475">Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .</span><span class="sxs-lookup"><span data-stu-id="e1a6e-475">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1a6e-476">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e1a6e-476">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="e1a6e-477">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="e1a6e-477">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="e1a6e-478">Microsoft Azure: Key Vault documentação</span><span class="sxs-lookup"><span data-stu-id="e1a6e-478">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="e1a6e-479">Como gerar e transferir chaves protegidas por HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e1a6e-479">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="e1a6e-480">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="e1a6e-480">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="e1a6e-481">Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET</span><span class="sxs-lookup"><span data-stu-id="e1a6e-481">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="e1a6e-482">Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET</span><span class="sxs-lookup"><span data-stu-id="e1a6e-482">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
