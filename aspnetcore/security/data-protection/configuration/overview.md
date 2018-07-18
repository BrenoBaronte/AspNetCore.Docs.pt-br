---
title: Configurar a proteção de dados do ASP.NET Core
author: rick-anderson
description: Saiba como configurar a proteção de dados no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/17/2017
uid: security/data-protection/configuration/overview
ms.openlocfilehash: f3cac3541ffe633886f82cec8180a219272c24d6
ms.sourcegitcommit: 3ca527f27c88cfc9d04688db5499e372fbc2c775
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39095594"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="afdcf-103">Configurar a proteção de dados do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="afdcf-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="afdcf-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="afdcf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="afdcf-105">Quando o sistema de proteção de dados é inicializado, ele se aplica [as configurações padrão](xref:security/data-protection/configuration/default-settings) com base no ambiente operacional.</span><span class="sxs-lookup"><span data-stu-id="afdcf-105">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="afdcf-106">Essas configurações são geralmente apropriadas para aplicativos em execução em um único computador.</span><span class="sxs-lookup"><span data-stu-id="afdcf-106">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="afdcf-107">Há casos em que um desenvolvedor talvez queira alterar as configurações padrão:</span><span class="sxs-lookup"><span data-stu-id="afdcf-107">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="afdcf-108">O aplicativo é distribuído entre várias máquinas.</span><span class="sxs-lookup"><span data-stu-id="afdcf-108">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="afdcf-109">Por motivos de conformidade.</span><span class="sxs-lookup"><span data-stu-id="afdcf-109">For compliance reasons.</span></span>

<span data-ttu-id="afdcf-110">Para esses cenários, o sistema de proteção de dados oferece uma API de configuração avançada.</span><span class="sxs-lookup"><span data-stu-id="afdcf-110">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="afdcf-111">Semelhante aos arquivos de configuração, o anel de chave de proteção de dados devem ser protegido usando as permissões apropriadas.</span><span class="sxs-lookup"><span data-stu-id="afdcf-111">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="afdcf-112">Você pode optar por criptografar as chaves em repouso, mas isso não impede que os invasores desde a criação de novas chaves.</span><span class="sxs-lookup"><span data-stu-id="afdcf-112">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="afdcf-113">Consequentemente, a segurança desse aplicativo é afetada.</span><span class="sxs-lookup"><span data-stu-id="afdcf-113">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="afdcf-114">O local de armazenamento configurado com proteção de dados deve ter seu acesso limitado ao próprio aplicativo, semelhante à forma como você protegerá os arquivos de configuração.</span><span class="sxs-lookup"><span data-stu-id="afdcf-114">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="afdcf-115">Por exemplo, se você optar por armazenar seu token de autenticação no disco, use as permissões do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="afdcf-115">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="afdcf-116">Certifique-se apenas a identidade sob a qual seu aplicativo web é executado tem ler, gravar e criar acesso a esse diretório.</span><span class="sxs-lookup"><span data-stu-id="afdcf-116">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="afdcf-117">Se você usar o armazenamento de tabelas do Azure, apenas o aplicativo web deve ter a capacidade de ler, gravar ou criar novas entradas no repositório de tabela, etc.</span><span class="sxs-lookup"><span data-stu-id="afdcf-117">If you use Azure Table Storage, only the web app should have the ability to read, write, or create new entries in the table store, etc.</span></span>
>
> <span data-ttu-id="afdcf-118">O método de extensão [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) retorna um [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="afdcf-118">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="afdcf-119">`IDataProtectionBuilder` expõe métodos de extensão que você pode encadear configurar a proteção de dados de opções.</span><span class="sxs-lookup"><span data-stu-id="afdcf-119">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="afdcf-120">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="afdcf-120">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="afdcf-121">Para armazenar as chaves na [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure o sistema com [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) no `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="afdcf-121">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="afdcf-122">Defina o local de armazenamento do anel de chave (por exemplo, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="afdcf-122">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="afdcf-123">O local deve ser definido porque chamando `ProtectKeysWithAzureKeyVault` implementa uma [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) que desabilita as configurações de proteção automática de dados, incluindo o local de armazenamento do anel de chave.</span><span class="sxs-lookup"><span data-stu-id="afdcf-123">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="afdcf-124">O exemplo anterior usa o armazenamento de BLOBs do Azure para persistir o anel de chave.</span><span class="sxs-lookup"><span data-stu-id="afdcf-124">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="afdcf-125">Para obter mais informações, consulte [principais provedores de armazenamento: o Azure e Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis).</span><span class="sxs-lookup"><span data-stu-id="afdcf-125">For more information, see [Key storage providers: Azure and Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis).</span></span> <span data-ttu-id="afdcf-126">Também é possível persistir o token de autenticação localmente com [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="afdcf-126">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="afdcf-127">O `keyIdentifier` é o identificador de chave de Cofre de chaves usado para criptografia de chave (por exemplo, `https://contosokeyvault.vault.azure.net/keys/dataprotection/`).</span><span class="sxs-lookup"><span data-stu-id="afdcf-127">The `keyIdentifier` is the key vault key identifier used for key encryption (for example, `https://contosokeyvault.vault.azure.net/keys/dataprotection/`).</span></span>

<span data-ttu-id="afdcf-128">`ProtectKeysWithAzureKeyVault` sobrecargas:</span><span class="sxs-lookup"><span data-stu-id="afdcf-128">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="afdcf-129">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permite o uso de um [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) para permitir que o sistema de proteção de dados usar o Cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="afdcf-129">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="afdcf-130">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, cadeia de caracteres, cadeia de caracteres, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permite o uso de um `ClientId` e [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) para permitir que o sistema de proteção de dados usar o Cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="afdcf-130">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="afdcf-131">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permite o uso de um `ClientId` e `ClientSecret` para permitir que o sistema de proteção de dados usar o Cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="afdcf-131">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="afdcf-132">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="afdcf-132">PersistKeysToFileSystem</span></span>

<span data-ttu-id="afdcf-133">Para armazenar chaves em um compartilhamento UNC, em vez da a *% LOCALAPPDATA %* local padrão, configure o sistema com [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="afdcf-133">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="afdcf-134">Se você alterar o local de persistência de chave, o sistema não criptografa automaticamente os chaves em repouso, já que ele não sabe se o DPAPI é um mecanismo de criptografia apropriado.</span><span class="sxs-lookup"><span data-stu-id="afdcf-134">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="afdcf-135">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="afdcf-135">ProtectKeysWith\*</span></span>

<span data-ttu-id="afdcf-136">Você pode configurar o sistema para proteger as chaves em repouso chamando o [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) APIs de configuração.</span><span class="sxs-lookup"><span data-stu-id="afdcf-136">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="afdcf-137">Considere o exemplo a seguir, que armazena as chaves em um compartilhamento UNC e criptografa essas chaves em repouso com um certificado x. 509 específico:</span><span class="sxs-lookup"><span data-stu-id="afdcf-137">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

<span data-ttu-id="afdcf-138">Ver [chave de criptografia em repouso](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais exemplos e uma discussão sobre os mecanismos de criptografia de chave interna.</span><span class="sxs-lookup"><span data-stu-id="afdcf-138">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="afdcf-139">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="afdcf-139">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="afdcf-140">Para configurar o sistema para usar uma vida útil de 14 dias, em vez do padrão 90 dias, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="afdcf-140">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="afdcf-141">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="afdcf-141">SetApplicationName</span></span>

<span data-ttu-id="afdcf-142">Por padrão, o sistema de proteção de dados isola os aplicativos uns dos outros, mesmo que compartilham o mesmo repositório de chave físico.</span><span class="sxs-lookup"><span data-stu-id="afdcf-142">By default, the Data Protection system isolates apps from one another, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="afdcf-143">Isso impede que os aplicativos Noções básicas sobre cargas protegidas uns dos outros.</span><span class="sxs-lookup"><span data-stu-id="afdcf-143">This prevents the apps from understanding each other's protected payloads.</span></span> <span data-ttu-id="afdcf-144">Para compartilhar cargas protegidas entre dois aplicativos, use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) com o mesmo valor para cada aplicativo:</span><span class="sxs-lookup"><span data-stu-id="afdcf-144">To share protected payloads between two apps, use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) with the same value for each app:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="afdcf-145">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="afdcf-145">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="afdcf-146">Você pode ter um cenário onde você não deseja que um aplicativo para reverter automaticamente chaves (criar novas chaves), como eles abordam a expiração.</span><span class="sxs-lookup"><span data-stu-id="afdcf-146">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="afdcf-147">Um exemplo disso pode ser configurados em uma relação primária/secundária, em que apenas o aplicativo principal é responsável por questões de gerenciamento de chaves e aplicativos secundários simplesmente tem uma exibição somente leitura do anel de chave de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="afdcf-147">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="afdcf-148">Os aplicativos secundários podem ser configurados para tratar o anel de chave como somente leitura ao configurar o sistema com [DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):</span><span class="sxs-lookup"><span data-stu-id="afdcf-148">The secondary apps can be configured to treat the key ring as read-only by configuring the system with [DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="afdcf-149">Isolamento por aplicativo</span><span class="sxs-lookup"><span data-stu-id="afdcf-149">Per-application isolation</span></span>

<span data-ttu-id="afdcf-150">Quando o sistema de proteção de dados é fornecido por um host do ASP.NET Core, ele automaticamente isola os aplicativos uns dos outros, mesmo que esses aplicativos estão em execução na mesma conta de processo de trabalho e estiver usando o mesmo material de chave mestra.</span><span class="sxs-lookup"><span data-stu-id="afdcf-150">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="afdcf-151">Isso é um pouco semelhante ao modificador IsolateApps partir do System. Web  **\<machineKey >** elemento.</span><span class="sxs-lookup"><span data-stu-id="afdcf-151">This is somewhat similar to the IsolateApps modifier from System.Web's **\<machineKey>** element.</span></span>

<span data-ttu-id="afdcf-152">O mecanismo de isolamento funciona considerando cada aplicativo no computador local como um locatário exclusivo, portanto, o [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) com raiz para qualquer aplicativo em particular automaticamente inclui a ID do aplicativo como um discriminador.</span><span class="sxs-lookup"><span data-stu-id="afdcf-152">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="afdcf-153">A ID do aplicativo exclusivo vem de um dos dois locais:</span><span class="sxs-lookup"><span data-stu-id="afdcf-153">The app's unique ID comes from one of two places:</span></span>

1. <span data-ttu-id="afdcf-154">Se o aplicativo estiver hospedado no IIS, o identificador exclusivo é o caminho de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="afdcf-154">If the app is hosted in IIS, the unique identifier is the app's configuration path.</span></span> <span data-ttu-id="afdcf-155">Se um aplicativo é implantado em um ambiente de farm da web, esse valor deve ser estável, supondo que os ambientes de IIS são configurados da mesma forma em todas as máquinas no web farm.</span><span class="sxs-lookup"><span data-stu-id="afdcf-155">If an app is deployed in a web farm environment, this value should be stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>

2. <span data-ttu-id="afdcf-156">Se o aplicativo não está hospedado no IIS, o identificador exclusivo é o caminho físico do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="afdcf-156">If the app isn't hosted in IIS, the unique identifier is the physical path of the app.</span></span>

<span data-ttu-id="afdcf-157">O identificador exclusivo é projetado para sobreviver a reinicializações &mdash; do aplicativo individual e a própria máquina.</span><span class="sxs-lookup"><span data-stu-id="afdcf-157">The unique identifier is designed to survive resets &mdash; both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="afdcf-158">Esse mecanismo de isolamento pressupõe que os aplicativos não são mal-intencionados.</span><span class="sxs-lookup"><span data-stu-id="afdcf-158">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="afdcf-159">Um aplicativo mal-intencionado sempre pode afetar qualquer outro aplicativo em execução sob a mesma conta de processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="afdcf-159">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="afdcf-160">Em um ambiente de hospedagem compartilhado em que os aplicativos são mutuamente não confiáveis, o provedor de hospedagem deve tomar medidas para garantir o isolamento do nível de sistema operacional entre aplicativos, incluindo a separação de repositórios de chave de base de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="afdcf-160">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="afdcf-161">Se o sistema de proteção de dados não é fornecido por um host do ASP.NET Core (por exemplo, se você instanciá-la por meio de `DataProtectionProvider` tipo concreto) isolamento de aplicativo está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="afdcf-161">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="afdcf-162">Quando o isolamento de aplicativo está desabilitado, todos os aplicativos de apoio com o mesmo material de chaveamento podem compartilhar cargas desde que eles fornecem apropriado [fins](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="afdcf-162">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="afdcf-163">Para fornecer isolamento de aplicativo nesse ambiente, chame o [SetApplicationName](#setapplicationname) método na configuração do objeto e forneça um nome exclusivo para cada aplicativo.</span><span class="sxs-lookup"><span data-stu-id="afdcf-163">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="afdcf-164">Alterando os algoritmos com UseCryptographicAlgorithms</span><span class="sxs-lookup"><span data-stu-id="afdcf-164">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="afdcf-165">A pilha da proteção de dados permite que você altere o algoritmo padrão usado por chaves recentemente geradas.</span><span class="sxs-lookup"><span data-stu-id="afdcf-165">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="afdcf-166">A maneira mais simples de fazer isso é chamar [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) do retorno de chamada de configuração:</span><span class="sxs-lookup"><span data-stu-id="afdcf-166">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="afdcf-167">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="afdcf-167">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="afdcf-168">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="afdcf-168">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

<span data-ttu-id="afdcf-169">O padrão EncryptionAlgorithm é AES-256-CBC e o padrão ValidationAlgorithm é HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="afdcf-169">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="afdcf-170">A política padrão pode ser definida por um administrador do sistema por meio de um [política de todo o computador](xref:security/data-protection/configuration/machine-wide-policy), mas uma chamada explícita para `UseCryptographicAlgorithms` substitui a política padrão.</span><span class="sxs-lookup"><span data-stu-id="afdcf-170">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="afdcf-171">Chamar `UseCryptographicAlgorithms` permite que você especifique o algoritmo desejado em uma lista predefinida de interna.</span><span class="sxs-lookup"><span data-stu-id="afdcf-171">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="afdcf-172">Você não precisa se preocupar sobre a implementação do algoritmo.</span><span class="sxs-lookup"><span data-stu-id="afdcf-172">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="afdcf-173">No cenário acima, o sistema de proteção de dados tenta usar a implementação de CNG do AES se em execução no Windows.</span><span class="sxs-lookup"><span data-stu-id="afdcf-173">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="afdcf-174">Caso contrário, ele reverterá para o gerenciado [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) classe.</span><span class="sxs-lookup"><span data-stu-id="afdcf-174">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="afdcf-175">Você pode especificar manualmente uma implementação por meio de uma chamada para [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="afdcf-175">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="afdcf-176">Algoritmos a alteração não afeta as chaves existentes do anel de chave.</span><span class="sxs-lookup"><span data-stu-id="afdcf-176">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="afdcf-177">Ela afeta apenas as chaves recentemente geradas.</span><span class="sxs-lookup"><span data-stu-id="afdcf-177">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="afdcf-178">Especificando algoritmos gerenciados personalizados</span><span class="sxs-lookup"><span data-stu-id="afdcf-178">Specifying custom managed algorithms</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="afdcf-179">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="afdcf-179">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="afdcf-180">Para especificar algoritmos gerenciados personalizados, crie uma [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instância que aponta para os tipos de implementação:</span><span class="sxs-lookup"><span data-stu-id="afdcf-180">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="afdcf-181">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="afdcf-181">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="afdcf-182">Para especificar algoritmos gerenciados personalizados, crie uma [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instância que aponta para os tipos de implementação:</span><span class="sxs-lookup"><span data-stu-id="afdcf-182">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

---

<span data-ttu-id="afdcf-183">Geralmente, o \*propriedades de tipo devem apontar para concreto, implementações que pode ser instanciadas (por meio de um construtor sem parâmetros público) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) e [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), embora o sistema classifica como caso especial, como alguns valores `typeof(Aes)` para sua conveniência.</span><span class="sxs-lookup"><span data-stu-id="afdcf-183">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="afdcf-184">O SymmetricAlgorithm deve ter um comprimento de chave de 128 bits ≥ e um tamanho de bloco de 64 bits do ≥, e ele deve oferecer suporte à criptografia de modo CBC com preenchimento PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="afdcf-184">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="afdcf-185">O KeyedHashAlgorithm deve ter um tamanho de resumo de > = 128 bits, e ele deve oferecer suporte a chaves de comprimento igual ao comprimento do resumo do algoritmo de hash.</span><span class="sxs-lookup"><span data-stu-id="afdcf-185">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="afdcf-186">O KeyedHashAlgorithm não é estritamente necessária para ser HMAC.</span><span class="sxs-lookup"><span data-stu-id="afdcf-186">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="afdcf-187">Especificando algoritmos personalizados da CNG do Windows</span><span class="sxs-lookup"><span data-stu-id="afdcf-187">Specifying custom Windows CNG algorithms</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="afdcf-188">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="afdcf-188">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="afdcf-189">Para especificar um algoritmo personalizado do CNG do Windows usando a criptografia de modo CBC com validação do HMAC, crie uma [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instância que contém as informações de algoritmos:</span><span class="sxs-lookup"><span data-stu-id="afdcf-189">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="afdcf-190">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="afdcf-190">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="afdcf-191">Para especificar um algoritmo personalizado do CNG do Windows usando a criptografia de modo CBC com validação do HMAC, crie uma [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instância que contém as informações de algoritmos:</span><span class="sxs-lookup"><span data-stu-id="afdcf-191">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

---

> [!NOTE]
> <span data-ttu-id="afdcf-192">O algoritmo de criptografia de bloco simétricas deve ter um comprimento de chave de > = 128 bits, um tamanho de bloco de > = 64 bits, e ele deve oferecer suporte à criptografia de modo CBC com preenchimento PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="afdcf-192">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="afdcf-193">O algoritmo de hash deve ter um tamanho de resumo de > = 128 bits e deve oferecer suporte a que está sendo aberto com o BCRYPT\_ALG\_MANIPULAR\_HMAC\_sinalizador de sinalizador.</span><span class="sxs-lookup"><span data-stu-id="afdcf-193">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="afdcf-194">O \*propriedades do provedor podem ser definidas como nulas para usar o provedor padrão para o algoritmo especificado.</span><span class="sxs-lookup"><span data-stu-id="afdcf-194">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="afdcf-195">Consulte a [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentação para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="afdcf-195">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="afdcf-196">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="afdcf-196">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="afdcf-197">Para especificar um algoritmo personalizado do CNG do Windows usando a criptografia de modo Galois/contador com a validação, crie uma [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instância que contém as informações de algoritmos:</span><span class="sxs-lookup"><span data-stu-id="afdcf-197">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="afdcf-198">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="afdcf-198">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="afdcf-199">Para especificar um algoritmo personalizado do CNG do Windows usando a criptografia de modo Galois/contador com a validação, crie uma [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instância que contém as informações de algoritmos:</span><span class="sxs-lookup"><span data-stu-id="afdcf-199">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

---

> [!NOTE]
> <span data-ttu-id="afdcf-200">O algoritmo de criptografia de bloco simétricas deve ter um comprimento de chave de > = 128 bits, um tamanho de bloco de exatamente de 128 bits, e ele deve oferecer suporte a criptografia do GCM.</span><span class="sxs-lookup"><span data-stu-id="afdcf-200">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="afdcf-201">Você pode definir as [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) propriedade como nulo para usar o provedor padrão para o algoritmo especificado.</span><span class="sxs-lookup"><span data-stu-id="afdcf-201">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="afdcf-202">Consulte a [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentação para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="afdcf-202">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="afdcf-203">Especificar outros algoritmos personalizados</span><span class="sxs-lookup"><span data-stu-id="afdcf-203">Specifying other custom algorithms</span></span>

<span data-ttu-id="afdcf-204">Embora não é exposta como uma API de primeira classe, o sistema de proteção de dados é extensível o suficiente para permitir a especificação de praticamente qualquer tipo de algoritmo.</span><span class="sxs-lookup"><span data-stu-id="afdcf-204">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="afdcf-205">Por exemplo, é possível manter todas as chaves contidas dentro do módulo de segurança um Hardware (HSM) e fornecer uma implementação personalizada do núcleo rotinas de criptografia e descriptografia.</span><span class="sxs-lookup"><span data-stu-id="afdcf-205">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="afdcf-206">Ver [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) na [principais de extensibilidade da criptografia](xref:security/data-protection/extensibility/core-crypto) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="afdcf-206">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="afdcf-207">Chaves persistentes ao hospedar em um contêiner do Docker</span><span class="sxs-lookup"><span data-stu-id="afdcf-207">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="afdcf-208">Ao hospedar em um [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) contêiner, as chaves devem ser mantidas em qualquer um:</span><span class="sxs-lookup"><span data-stu-id="afdcf-208">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="afdcf-209">Uma pasta que é um volume do Docker que persiste além do tempo de vida do contêiner, como um volume compartilhado ou um volume montado pelo host.</span><span class="sxs-lookup"><span data-stu-id="afdcf-209">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="afdcf-210">Um provedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [Redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="afdcf-210">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="see-also"></a><span data-ttu-id="afdcf-211">Consulte também</span><span class="sxs-lookup"><span data-stu-id="afdcf-211">See also</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
