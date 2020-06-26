---
title: Provedores de armazenamento de chaves no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre os principais provedores de armazenamento no ASP.NET Core e como configurar locais de armazenamento de chaves.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 1bbea6f16d57d5cc107c95293e2788271bfce601
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408039"
---
# <a name="key-storage-providers-in-aspnet-core"></a>Provedores de armazenamento de chaves no ASP.NET Core

O sistema de proteção de dados [emprega um mecanismo de descoberta por padrão](xref:security/data-protection/configuration/default-settings) para determinar onde as chaves de criptografia devem ser persistentes. O desenvolvedor pode substituir o mecanismo de descoberta padrão e especificar manualmente o local.

> [!WARNING]
> Se você especificar um local de persistência de chave explícita, o sistema de proteção de dados cancelará o registro do mecanismo de criptografia de chave padrão em repouso, de modo que as chaves não sejam mais criptografadas em repouso. É recomendável que você [especifique também um mecanismo de criptografia de chave explícito](xref:security/data-protection/implementation/key-encryption-at-rest) para implantações de produção.

## <a name="file-system"></a>Sistema de arquivos

Para configurar um repositório de chaves baseado em sistema de arquivos, chame a rotina de configuração do [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , conforme mostrado abaixo. Forneça um [DirectoryInfo](/dotnet/api/system.io.directoryinfo) apontando para o repositório onde as chaves devem ser armazenadas:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

O `DirectoryInfo` pode apontar para um diretório no computador local ou pode apontar para uma pasta em um compartilhamento de rede. Se estiver apontando para um diretório no computador local (e o cenário é que somente os aplicativos no computador local exigem acesso para usar esse repositório), considere usar o [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (no Windows) para criptografar as chaves em repouso. Caso contrário, considere o uso de um [certificado X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) para criptografar chaves em repouso.

## <a name="azure-storage"></a>Armazenamento do Azure

O pacote [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) permite armazenar chaves de proteção de dados no armazenamento de BLOBs do Azure. As chaves podem ser compartilhadas entre várias instâncias de um aplicativo Web. Os aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.

Para configurar o provedor de armazenamento de BLOBs do Azure, chame uma das sobrecargas de [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Se o aplicativo Web estiver sendo executado como um serviço do Azure, os tokens de autenticação poderão ser criados automaticamente usando [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

Veja [mais detalhes sobre como configurar a autenticação de serviço a serviço.](/azure/key-vault/service-to-service-authentication)

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

O pacote [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) permite armazenar chaves de proteção de dados em um cache Redis. As chaves podem ser compartilhadas entre várias instâncias de um aplicativo Web. Os aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O pacote [Microsoft. AspNetCore. dataprotection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) permite armazenar chaves de proteção de dados em um cache Redis. As chaves podem ser compartilhadas entre várias instâncias de um aplicativo Web. Os aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Para configurar o no Redis, chame uma das sobrecargas de [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Para configurar o no Redis, chame uma das sobrecargas de [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Para obter mais informações, consulte estes tópicos:

* [StackExchange. Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Cache Redis do Azure](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [Exemplos de proteção de ASP.NET Core dataprotection](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Registro

**Aplica-se somente a implantações do Windows.**

Às vezes, o aplicativo pode não ter acesso de gravação ao sistema de arquivos. Considere um cenário em que um aplicativo está sendo executado como uma conta de serviço virtual (como a identidade do pool de aplicativos do *w3wp.exe*). Nesses casos, o administrador pode provisionar uma chave do registro acessível pela identidade da conta de serviço. Chame o método de extensão [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , conforme mostrado abaixo. Forneça uma [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) apontando para o local onde as chaves de criptografia devem ser armazenadas:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> É recomendável usar o [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) para criptografar as chaves em repouso.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

O pacote [Microsoft. AspNetCore. dataprotection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) fornece um mecanismo para armazenar as chaves de proteção de dados em um banco usando Entity Framework Core. O `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` pacote NuGet deve ser adicionado ao arquivo de projeto, ele não faz parte do [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Com esse pacote, as chaves podem ser compartilhadas entre várias instâncias de um aplicativo Web.

Para configurar o provedor de EF Core, chame o método [PersistKeysToDbContext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

O parâmetro genérico, `TContext` , deve herdar de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e implementar [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Crie a `DataProtectionKeys` tabela.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Execute os seguintes comandos na janela do **console do Gerenciador de pacotes** (PMC):

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Execute os seguintes comandos em um shell de comando:

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext`é o `DbContext` definido no exemplo de código anterior. Se você estiver usando um `DbContext` com um nome diferente, substitua seu `DbContext` nome por `MyKeysContext` .

A `DataProtectionKeys` classe/entidade adota a estrutura mostrada na tabela a seguir.

| Propriedade/campo | Tipo CLR | Tipo SQL              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, CP, `IDENTITY(1,1)` , não nulo   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, nulo |
| `Xml`          | `string` | `nvarchar(MAX)`, nulo |

::: moniker-end

## <a name="custom-key-repository"></a>Repositório de chaves personalizado

Se os mecanismos na caixa não forem apropriados, o desenvolvedor poderá especificar seu próprio mecanismo de persistência de chave fornecendo um [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)personalizado.
