---
title: Azure Key Vault provedor de configuração no ASP.NET Core
author: rick-anderson
description: Saiba como usar o provedor de configuração Azure Key Vault para configurar um aplicativo usando pares de nome-valor carregados em tempo de execução.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/key-vault-configuration
ms.openlocfilehash: 47172339f1c82a572a8a2c5d4ba49e4906e30b29
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406869"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="43ace-103">Azure Key Vault provedor de configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43ace-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="43ace-104">Por [Andrew Stanton-enfermaria](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="43ace-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43ace-105">Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="43ace-106">Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="43ace-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="43ace-107">Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="43ace-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="43ace-108">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="43ace-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="43ace-109">Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="43ace-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="43ace-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43ace-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="43ace-111">Pacotes</span><span class="sxs-lookup"><span data-stu-id="43ace-111">Packages</span></span>

<span data-ttu-id="43ace-112">Adicione uma referência de pacote ao [Microsoft.Extensions.Configuração. Pacote AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="43ace-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="43ace-113">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="43ace-113">Sample app</span></span>

<span data-ttu-id="43ace-114">O aplicativo de exemplo é executado em um dos dois modos determinados pela `#define` instrução na parte superior do arquivo *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="43ace-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="43ace-115">`Certificate`: Demonstra o uso de uma Azure Key Vault ID do cliente e do certificado X. 509 para acessar os segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="43ace-116">Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43ace-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="43ace-117">`Managed`: Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="43ace-118">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="43ace-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="43ace-119">A `Managed` versão do exemplo deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="43ace-120">Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="43ace-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="43ace-121">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador ( `#define` ), consulte <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="43ace-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="43ace-122">Armazenamento de segredo no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="43ace-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="43ace-123">Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="43ace-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="43ace-124">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.</span><span class="sxs-lookup"><span data-stu-id="43ace-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="43ace-125">A ferramenta Gerenciador de segredo requer uma `<UserSecretsId>` propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="43ace-126">Defina o valor da propriedade ( `{GUID}` ) para qualquer Guid exclusivo:</span><span class="sxs-lookup"><span data-stu-id="43ace-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="43ace-127">Os segredos são criados como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="43ace-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="43ace-128">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="43ace-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="43ace-129">O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="43ace-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="43ace-130">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="43ace-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="43ace-131">Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para `_prod` .</span><span class="sxs-lookup"><span data-stu-id="43ace-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="43ace-132">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="43ace-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="43ace-133">Armazenamento de segredo no ambiente de produção com Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="43ace-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="43ace-134">As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="43ace-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="43ace-135">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="43ace-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="43ace-136">Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="43ace-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="43ace-137">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="43ace-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="43ace-138">Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="43ace-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="43ace-139">Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="43ace-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="43ace-140">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="43ace-141">Para obter mais informações, consulte [CLI do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="43ace-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="43ace-142">Se você ainda não estiver autenticado, entre com o `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="43ace-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="43ace-143">Crie um grupo de recursos com o seguinte comando, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="43ace-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="43ace-144">Crie um cofre de chaves no grupo de recursos com o seguinte comando, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="43ace-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="43ace-145">Crie segredos no cofre de chaves como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="43ace-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="43ace-146">Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="43ace-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="43ace-147">Os valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="43ace-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="43ace-148">Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="43ace-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="43ace-149">Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="43ace-150">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="43ace-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="43ace-151">Os valores incluem um `_prod` sufixo para distingui-los dos `_dev` valores de sufixo carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="43ace-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="43ace-152">Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="43ace-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="43ace-153">Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="43ace-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="43ace-154">Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="43ace-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="43ace-155">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="43ace-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="43ace-156">Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="43ace-157">Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="43ace-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="43ace-158">O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="43ace-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="43ace-159">Crie um certificado PKCS # 12 arquivo morto (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="43ace-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="43ace-160">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="43ace-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="43ace-161">Instale o certificado no repositório de certificados pessoal do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="43ace-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="43ace-162">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="43ace-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="43ace-163">Observe a impressão digital do certificado, que é usada posteriormente neste processo.</span><span class="sxs-lookup"><span data-stu-id="43ace-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="43ace-164">Exporte o certificado PKCS # 12 Archive (*. pfx*) como um certificado codificado em der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="43ace-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="43ace-165">Registre o aplicativo com o Azure AD (**registros de aplicativo**).</span><span class="sxs-lookup"><span data-stu-id="43ace-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="43ace-166">Carregue o certificado codificado em DER (*. cer*) no Azure AD:</span><span class="sxs-lookup"><span data-stu-id="43ace-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="43ace-167">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="43ace-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="43ace-168">Navegue até **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="43ace-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="43ace-169">Selecione **carregar certificado** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="43ace-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="43ace-170">Um certificado *. cer*, *. pem*ou *. CRT* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="43ace-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="43ace-171">Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado noappsettings.jsdo aplicativo *no* arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ace-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="43ace-172">Navegue até **cofres de chaves** na portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="43ace-173">Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="43ace-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="43ace-174">Selecione **Políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="43ace-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="43ace-175">Selecione **Adicionar Política de Acesso**.</span><span class="sxs-lookup"><span data-stu-id="43ace-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="43ace-176">Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .</span><span class="sxs-lookup"><span data-stu-id="43ace-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="43ace-177">Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="43ace-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="43ace-178">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="43ace-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="43ace-179">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="43ace-179">Select **OK**.</span></span>
1. <span data-ttu-id="43ace-180">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="43ace-180">Select **Save**.</span></span>
1. <span data-ttu-id="43ace-181">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-181">Deploy the app.</span></span>

<span data-ttu-id="43ace-182">O `Certificate` aplicativo de exemplo obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="43ace-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="43ace-183">Valores não hierárquicos: o valor para `SecretName` é obtido com `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="43ace-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="43ace-184">Valores hierárquicos (seções): `:` notação de uso (dois-pontos) ou o `GetSection` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="43ace-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="43ace-185">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="43ace-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="43ace-186">O certificado X. 509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="43ace-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="43ace-187">O aplicativo chama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> com valores fornecidos pelo *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="43ace-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="43ace-188">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="43ace-188">Example values:</span></span>

* <span data-ttu-id="43ace-189">Nome do cofre de chaves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="43ace-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="43ace-190">ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="43ace-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="43ace-191">Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="43ace-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="43ace-192">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="43ace-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="43ace-193">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="43ace-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="43ace-194">No ambiente de desenvolvimento, os valores secretos são carregados com o `_dev` sufixo.</span><span class="sxs-lookup"><span data-stu-id="43ace-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="43ace-195">No ambiente de produção, os valores são carregados com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="43ace-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="43ace-196">Usar identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="43ace-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="43ace-197">**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="43ace-198">O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Managed` .</span><span class="sxs-lookup"><span data-stu-id="43ace-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="43ace-199">Insira o nome do cofre naappsettings.jsdo aplicativo *no* arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ace-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="43ace-200">O aplicativo de exemplo não requer uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a `Managed` versão, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="43ace-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="43ace-201">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado na *appsettings.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ace-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="43ace-202">Implante o aplicativo de exemplo no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="43ace-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="43ace-203">Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="43ace-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="43ace-204">Obtenha a ID de objeto da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="43ace-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="43ace-205">A ID de objeto é mostrada no portal do Azure no **Identity** painel do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="43ace-206">Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo `list` e `get` permissões para acessar o cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="43ace-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="43ace-207">**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="43ace-208">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="43ace-208">The sample app:</span></span>

* <span data-ttu-id="43ace-209">Cria uma instância da `AzureServiceTokenProvider` classe sem uma cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="43ace-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="43ace-210">Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="43ace-211">Um novo <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é criado com o `AzureServiceTokenProvider` retorno de chamada de token de instância.</span><span class="sxs-lookup"><span data-stu-id="43ace-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="43ace-212">A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com uma implementação padrão do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carrega todos os valores secretos e substitui traços duplos ( `--` ) por dois-pontos ( `:` ) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="43ace-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="43ace-213">Valor de exemplo do nome do cofre de chaves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="43ace-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="43ace-214">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="43ace-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="43ace-215">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="43ace-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="43ace-216">No ambiente de desenvolvimento, os valores secretos têm o `_dev` sufixo porque são fornecidos por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="43ace-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="43ace-217">No ambiente de produção, os valores são carregados com o `_prod` sufixo porque são fornecidos por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="43ace-218">Se você receber um `Access denied` erro, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="43ace-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="43ace-219">Confirme que você reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="43ace-220">Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="43ace-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="43ace-221">Opções de configuração</span><span class="sxs-lookup"><span data-stu-id="43ace-221">Configuration options</span></span>

<span data-ttu-id="43ace-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>pode aceitar um <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="43ace-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="43ace-223">Propriedade</span><span class="sxs-lookup"><span data-stu-id="43ace-223">Property</span></span>         | <span data-ttu-id="43ace-224">Descrição</span><span class="sxs-lookup"><span data-stu-id="43ace-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="43ace-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>para usar para recuperar valores.</span><span class="sxs-lookup"><span data-stu-id="43ace-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="43ace-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instância usada para controlar o carregamento de segredo.</span><span class="sxs-lookup"><span data-stu-id="43ace-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="43ace-227">`Timespan`aguardar entre as tentativas de sondagem do cofre de chaves para alterações.</span><span class="sxs-lookup"><span data-stu-id="43ace-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="43ace-228">O valor padrão é `null` (a configuração não é recarregada).</span><span class="sxs-lookup"><span data-stu-id="43ace-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="43ace-229">URI do Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="43ace-230">Usar um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="43ace-230">Use a key name prefix</span></span>

<span data-ttu-id="43ace-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornece uma sobrecarga que aceita uma implementação do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="43ace-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="43ace-232">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="43ace-233">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="43ace-234">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="43ace-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="43ace-235">Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="43ace-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="43ace-236">No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="43ace-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="43ace-237">Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="43ace-238">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para o `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="43ace-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="43ace-239">Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret` , eliminando a versão à medida que ela carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="43ace-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="43ace-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>é chamado com um personalizado <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="43ace-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="43ace-241">A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:</span><span class="sxs-lookup"><span data-stu-id="43ace-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="43ace-242">`Load`carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="43ace-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="43ace-243">Outros segredos não são carregados.</span><span class="sxs-lookup"><span data-stu-id="43ace-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="43ace-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="43ace-244">`GetKey`:</span></span>
  * <span data-ttu-id="43ace-245">Remove o prefixo do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="43ace-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="43ace-246">Substitui dois traços em qualquer nome pelo `KeyDelimiter` , que é o delimitador usado na configuração (geralmente um dois-pontos).</span><span class="sxs-lookup"><span data-stu-id="43ace-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="43ace-247">Azure Key Vault não permite dois pontos em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="43ace-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="43ace-248">O `Load` método é chamado por um algoritmo de provedor que itera por meio dos segredos do cofre para localizar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="43ace-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="43ace-249">Quando um prefixo de versão é encontrado com `Load` , o algoritmo usa o `GetKey` método para retornar o nome de configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="43ace-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="43ace-250">Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="43ace-251">Quando essa abordagem é implementada:</span><span class="sxs-lookup"><span data-stu-id="43ace-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="43ace-252">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="43ace-253">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="43ace-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="43ace-254">Confirme se uma `<UserSecretsId>` propriedade está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="43ace-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="43ace-255">Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="43ace-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="43ace-256">Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="43ace-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="43ace-257">Quando o aplicativo é executado, os segredos do cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="43ace-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="43ace-258">O segredo da cadeia de caracteres para `5000-AppSecret` é correspondido à versão do aplicativo especificada no arquivo de projeto do aplicativo ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="43ace-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="43ace-259">A versão, `5000` (com o Dash), é removida do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="43ace-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="43ace-260">Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="43ace-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="43ace-261">Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado estará `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="43ace-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="43ace-262">Você também pode fornecer sua própria <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementação para o <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="43ace-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="43ace-263">Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="43ace-264">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="43ace-264">Bind an array to a class</span></span>

<span data-ttu-id="43ace-265">O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="43ace-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="43ace-266">Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois-pontos ( `:` ), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="43ace-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="43ace-267">Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="43ace-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="43ace-268">Azure Key Vault chaves não podem usar dois-pontos como separador.</span><span class="sxs-lookup"><span data-stu-id="43ace-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="43ace-269">A abordagem descrita neste tópico usa traços duplos ( `--` ) como separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="43ace-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="43ace-270">As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="43ace-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="43ace-271">Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="43ace-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="43ace-272">Há dois literais de objeto definidos na `WriteTo` matriz que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:</span><span class="sxs-lookup"><span data-stu-id="43ace-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="43ace-273">A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a `--` notação de traço duplo () e os segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="43ace-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="43ace-274">Chave</span><span class="sxs-lookup"><span data-stu-id="43ace-274">Key</span></span> | <span data-ttu-id="43ace-275">Valor</span><span class="sxs-lookup"><span data-stu-id="43ace-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="43ace-276">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="43ace-276">Reload secrets</span></span>

<span data-ttu-id="43ace-277">Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="43ace-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="43ace-278">Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que o `Reload` seja executado.</span><span class="sxs-lookup"><span data-stu-id="43ace-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="43ace-279">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="43ace-279">Disabled and expired secrets</span></span>

<span data-ttu-id="43ace-280">Os segredos desabilitados e expirados lançam um <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="43ace-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="43ace-281">Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="43ace-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="43ace-282">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="43ace-282">Troubleshoot</span></span>

<span data-ttu-id="43ace-283">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="43ace-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="43ace-284">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="43ace-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="43ace-285">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="43ace-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="43ace-286">O cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="43ace-287">O aplicativo não está autorizado a acessar o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="43ace-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="43ace-288">A política de acesso não `Get` inclui `List` permissões e.</span><span class="sxs-lookup"><span data-stu-id="43ace-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="43ace-289">No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="43ace-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="43ace-290">O aplicativo tem o nome do cofre de chaves errado ( `KeyVaultName` ), a ID do aplicativo do Azure AD ( `AzureADApplicationId` ) ou a impressão digital do certificado do Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="43ace-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="43ace-291">A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="43ace-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="43ace-292">Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .</span><span class="sxs-lookup"><span data-stu-id="43ace-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43ace-293">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="43ace-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="43ace-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="43ace-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="43ace-295">Microsoft Azure: Key Vault documentação</span><span class="sxs-lookup"><span data-stu-id="43ace-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="43ace-296">Como gerar e transferir chaves protegidas por HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="43ace-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="43ace-297">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="43ace-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="43ace-298">Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET</span><span class="sxs-lookup"><span data-stu-id="43ace-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="43ace-299">Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET</span><span class="sxs-lookup"><span data-stu-id="43ace-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="43ace-300">Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="43ace-301">Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="43ace-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="43ace-302">Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="43ace-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="43ace-303">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="43ace-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="43ace-304">Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="43ace-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="43ace-305">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43ace-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="43ace-306">Pacotes</span><span class="sxs-lookup"><span data-stu-id="43ace-306">Packages</span></span>

<span data-ttu-id="43ace-307">Adicione uma referência de pacote ao [Microsoft.Extensions.Configuração. Pacote AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="43ace-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="43ace-308">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="43ace-308">Sample app</span></span>

<span data-ttu-id="43ace-309">O aplicativo de exemplo é executado em um dos dois modos determinados pela `#define` instrução na parte superior do arquivo *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="43ace-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="43ace-310">`Certificate`: Demonstra o uso de uma Azure Key Vault ID do cliente e do certificado X. 509 para acessar os segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-310">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="43ace-311">Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43ace-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="43ace-312">`Managed`: Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-312">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="43ace-313">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="43ace-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="43ace-314">A `Managed` versão do exemplo deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="43ace-315">Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="43ace-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="43ace-316">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador ( `#define` ), consulte <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="43ace-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="43ace-317">Armazenamento de segredo no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="43ace-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="43ace-318">Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="43ace-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="43ace-319">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.</span><span class="sxs-lookup"><span data-stu-id="43ace-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="43ace-320">A ferramenta Gerenciador de segredo requer uma `<UserSecretsId>` propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="43ace-321">Defina o valor da propriedade ( `{GUID}` ) para qualquer Guid exclusivo:</span><span class="sxs-lookup"><span data-stu-id="43ace-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="43ace-322">Os segredos são criados como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="43ace-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="43ace-323">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="43ace-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="43ace-324">O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="43ace-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="43ace-325">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="43ace-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="43ace-326">Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para `_prod` .</span><span class="sxs-lookup"><span data-stu-id="43ace-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="43ace-327">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="43ace-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="43ace-328">Armazenamento de segredo no ambiente de produção com Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="43ace-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="43ace-329">As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="43ace-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="43ace-330">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="43ace-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="43ace-331">Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="43ace-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="43ace-332">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="43ace-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="43ace-333">Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="43ace-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="43ace-334">Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="43ace-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="43ace-335">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="43ace-336">Para obter mais informações, consulte [CLI do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="43ace-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="43ace-337">Se você ainda não estiver autenticado, entre com o `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="43ace-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="43ace-338">Crie um grupo de recursos com o seguinte comando, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="43ace-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="43ace-339">Crie um cofre de chaves no grupo de recursos com o seguinte comando, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="43ace-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="43ace-340">Crie segredos no cofre de chaves como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="43ace-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="43ace-341">Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="43ace-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="43ace-342">Os valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="43ace-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="43ace-343">Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="43ace-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="43ace-344">Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="43ace-345">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="43ace-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="43ace-346">Os valores incluem um `_prod` sufixo para distingui-los dos `_dev` valores de sufixo carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="43ace-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="43ace-347">Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="43ace-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="43ace-348">Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="43ace-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="43ace-349">Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="43ace-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="43ace-350">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="43ace-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="43ace-351">Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="43ace-352">Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="43ace-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="43ace-353">O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="43ace-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="43ace-354">Crie um certificado PKCS # 12 arquivo morto (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="43ace-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="43ace-355">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="43ace-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="43ace-356">Instale o certificado no repositório de certificados pessoal do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="43ace-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="43ace-357">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="43ace-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="43ace-358">Observe a impressão digital do certificado, que é usada posteriormente neste processo.</span><span class="sxs-lookup"><span data-stu-id="43ace-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="43ace-359">Exporte o certificado PKCS # 12 Archive (*. pfx*) como um certificado codificado em der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="43ace-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="43ace-360">Registre o aplicativo com o Azure AD (**registros de aplicativo**).</span><span class="sxs-lookup"><span data-stu-id="43ace-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="43ace-361">Carregue o certificado codificado em DER (*. cer*) no Azure AD:</span><span class="sxs-lookup"><span data-stu-id="43ace-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="43ace-362">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="43ace-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="43ace-363">Navegue até **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="43ace-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="43ace-364">Selecione **carregar certificado** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="43ace-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="43ace-365">Um certificado *. cer*, *. pem*ou *. CRT* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="43ace-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="43ace-366">Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado noappsettings.jsdo aplicativo *no* arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ace-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="43ace-367">Navegue até **cofres de chaves** na portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="43ace-368">Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="43ace-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="43ace-369">Selecione **Políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="43ace-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="43ace-370">Selecione **Adicionar Política de Acesso**.</span><span class="sxs-lookup"><span data-stu-id="43ace-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="43ace-371">Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .</span><span class="sxs-lookup"><span data-stu-id="43ace-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="43ace-372">Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="43ace-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="43ace-373">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="43ace-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="43ace-374">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="43ace-374">Select **OK**.</span></span>
1. <span data-ttu-id="43ace-375">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="43ace-375">Select **Save**.</span></span>
1. <span data-ttu-id="43ace-376">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-376">Deploy the app.</span></span>

<span data-ttu-id="43ace-377">O `Certificate` aplicativo de exemplo obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="43ace-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="43ace-378">Valores não hierárquicos: o valor para `SecretName` é obtido com `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="43ace-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="43ace-379">Valores hierárquicos (seções): `:` notação de uso (dois-pontos) ou o `GetSection` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="43ace-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="43ace-380">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="43ace-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="43ace-381">O certificado X. 509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="43ace-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="43ace-382">O aplicativo chama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> com valores fornecidos pelo *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="43ace-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="43ace-383">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="43ace-383">Example values:</span></span>

* <span data-ttu-id="43ace-384">Nome do cofre de chaves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="43ace-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="43ace-385">ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="43ace-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="43ace-386">Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="43ace-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="43ace-387">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="43ace-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="43ace-388">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="43ace-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="43ace-389">No ambiente de desenvolvimento, os valores secretos são carregados com o `_dev` sufixo.</span><span class="sxs-lookup"><span data-stu-id="43ace-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="43ace-390">No ambiente de produção, os valores são carregados com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="43ace-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="43ace-391">Usar identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="43ace-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="43ace-392">**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="43ace-393">O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Managed` .</span><span class="sxs-lookup"><span data-stu-id="43ace-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="43ace-394">Insira o nome do cofre naappsettings.jsdo aplicativo *no* arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ace-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="43ace-395">O aplicativo de exemplo não requer uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a `Managed` versão, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="43ace-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="43ace-396">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado na *appsettings.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ace-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="43ace-397">Implante o aplicativo de exemplo no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="43ace-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="43ace-398">Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="43ace-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="43ace-399">Obtenha a ID de objeto da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="43ace-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="43ace-400">A ID de objeto é mostrada no portal do Azure no **Identity** painel do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="43ace-401">Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo `list` e `get` permissões para acessar o cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="43ace-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="43ace-402">**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="43ace-403">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="43ace-403">The sample app:</span></span>

* <span data-ttu-id="43ace-404">Cria uma instância da `AzureServiceTokenProvider` classe sem uma cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="43ace-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="43ace-405">Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="43ace-406">Um novo <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é criado com o `AzureServiceTokenProvider` retorno de chamada de token de instância.</span><span class="sxs-lookup"><span data-stu-id="43ace-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="43ace-407">A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com uma implementação padrão do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carrega todos os valores secretos e substitui traços duplos ( `--` ) por dois-pontos ( `:` ) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="43ace-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="43ace-408">Valor de exemplo do nome do cofre de chaves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="43ace-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="43ace-409">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="43ace-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="43ace-410">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="43ace-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="43ace-411">No ambiente de desenvolvimento, os valores secretos têm o `_dev` sufixo porque são fornecidos por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="43ace-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="43ace-412">No ambiente de produção, os valores são carregados com o `_prod` sufixo porque são fornecidos por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="43ace-413">Se você receber um `Access denied` erro, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="43ace-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="43ace-414">Confirme que você reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="43ace-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="43ace-415">Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="43ace-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="43ace-416">Usar um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="43ace-416">Use a key name prefix</span></span>

<span data-ttu-id="43ace-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornece uma sobrecarga que aceita uma implementação do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="43ace-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="43ace-418">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="43ace-419">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="43ace-420">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="43ace-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="43ace-421">Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="43ace-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="43ace-422">No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="43ace-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="43ace-423">Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="43ace-424">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para o `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="43ace-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="43ace-425">Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret` , eliminando a versão à medida que ela carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="43ace-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="43ace-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>é chamado com um personalizado <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="43ace-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="43ace-427">A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:</span><span class="sxs-lookup"><span data-stu-id="43ace-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="43ace-428">`Load`carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="43ace-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="43ace-429">Outros segredos não são carregados.</span><span class="sxs-lookup"><span data-stu-id="43ace-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="43ace-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="43ace-430">`GetKey`:</span></span>
  * <span data-ttu-id="43ace-431">Remove o prefixo do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="43ace-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="43ace-432">Substitui dois traços em qualquer nome pelo `KeyDelimiter` , que é o delimitador usado na configuração (geralmente um dois-pontos).</span><span class="sxs-lookup"><span data-stu-id="43ace-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="43ace-433">Azure Key Vault não permite dois pontos em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="43ace-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="43ace-434">O `Load` método é chamado por um algoritmo de provedor que itera por meio dos segredos do cofre para localizar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="43ace-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="43ace-435">Quando um prefixo de versão é encontrado com `Load` , o algoritmo usa o `GetKey` método para retornar o nome de configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="43ace-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="43ace-436">Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="43ace-437">Quando essa abordagem é implementada:</span><span class="sxs-lookup"><span data-stu-id="43ace-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="43ace-438">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="43ace-439">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="43ace-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="43ace-440">Confirme se uma `<UserSecretsId>` propriedade está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="43ace-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="43ace-441">Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="43ace-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="43ace-442">Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="43ace-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="43ace-443">Quando o aplicativo é executado, os segredos do cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="43ace-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="43ace-444">O segredo da cadeia de caracteres para `5000-AppSecret` é correspondido à versão do aplicativo especificada no arquivo de projeto do aplicativo ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="43ace-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="43ace-445">A versão, `5000` (com o Dash), é removida do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="43ace-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="43ace-446">Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="43ace-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="43ace-447">Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado estará `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="43ace-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="43ace-448">Você também pode fornecer sua própria <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementação para o <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="43ace-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="43ace-449">Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43ace-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="43ace-450">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="43ace-450">Bind an array to a class</span></span>

<span data-ttu-id="43ace-451">O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="43ace-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="43ace-452">Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois-pontos ( `:` ), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="43ace-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="43ace-453">Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="43ace-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="43ace-454">Azure Key Vault chaves não podem usar dois-pontos como separador.</span><span class="sxs-lookup"><span data-stu-id="43ace-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="43ace-455">A abordagem descrita neste tópico usa traços duplos ( `--` ) como separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="43ace-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="43ace-456">As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="43ace-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="43ace-457">Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="43ace-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="43ace-458">Há dois literais de objeto definidos na `WriteTo` matriz que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:</span><span class="sxs-lookup"><span data-stu-id="43ace-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="43ace-459">A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a `--` notação de traço duplo () e os segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="43ace-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="43ace-460">Chave</span><span class="sxs-lookup"><span data-stu-id="43ace-460">Key</span></span> | <span data-ttu-id="43ace-461">Valor</span><span class="sxs-lookup"><span data-stu-id="43ace-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="43ace-462">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="43ace-462">Reload secrets</span></span>

<span data-ttu-id="43ace-463">Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="43ace-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="43ace-464">Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que o `Reload` seja executado.</span><span class="sxs-lookup"><span data-stu-id="43ace-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="43ace-465">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="43ace-465">Disabled and expired secrets</span></span>

<span data-ttu-id="43ace-466">Os segredos desabilitados e expirados lançam um <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="43ace-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="43ace-467">Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="43ace-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="43ace-468">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="43ace-468">Troubleshoot</span></span>

<span data-ttu-id="43ace-469">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="43ace-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="43ace-470">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="43ace-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="43ace-471">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="43ace-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="43ace-472">O cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43ace-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="43ace-473">O aplicativo não está autorizado a acessar o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="43ace-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="43ace-474">A política de acesso não `Get` inclui `List` permissões e.</span><span class="sxs-lookup"><span data-stu-id="43ace-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="43ace-475">No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="43ace-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="43ace-476">O aplicativo tem o nome do cofre de chaves errado ( `KeyVaultName` ), a ID do aplicativo do Azure AD ( `AzureADApplicationId` ) ou a impressão digital do certificado do Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="43ace-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="43ace-477">A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="43ace-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="43ace-478">Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .</span><span class="sxs-lookup"><span data-stu-id="43ace-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43ace-479">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="43ace-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="43ace-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="43ace-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="43ace-481">Microsoft Azure: Key Vault documentação</span><span class="sxs-lookup"><span data-stu-id="43ace-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="43ace-482">Como gerar e transferir chaves protegidas por HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="43ace-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="43ace-483">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="43ace-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="43ace-484">Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET</span><span class="sxs-lookup"><span data-stu-id="43ace-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="43ace-485">Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET</span><span class="sxs-lookup"><span data-stu-id="43ace-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

