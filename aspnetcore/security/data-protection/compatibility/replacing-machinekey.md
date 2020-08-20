---
title: Substitua o ASP.NET machineKey no ASP.NET Core
author: rick-anderson
description: Descubra como substituir o machineKey em ASP.NET para permitir o uso de um sistema de proteção de dados novo e mais seguro.
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 8cae0b8f1c4582e272061ff87868b32568dfe595
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625564"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Substitua o ASP.NET machineKey no ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

A implementação do `<machineKey>` elemento em ASP.NET [é substituível](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Isso permite que a maioria das chamadas para rotinas de criptografia ASP.NET seja roteada por meio de um mecanismo de proteção de dados de substituição, incluindo o novo sistema de proteção de dados.

## <a name="package-installation"></a>Instalação do pacote

> [!NOTE]
> O novo sistema de proteção de dados só pode ser instalado em um aplicativo ASP.NET existente destinado ao .NET 4.5.1 ou posterior. A instalação falhará se o aplicativo for destinado ao .NET 4,5 ou inferior.

Para instalar o novo sistema de proteção de dados em um projeto existente do ASP.NET 4.5.1 +, instale o pacote Microsoft.AspNetCore.DataProtection.SystemWeb. Isso criará uma instância do sistema de proteção de dados usando as definições de [configuração padrão](xref:security/data-protection/configuration/default-settings) .

Quando você instala o pacote, ele insere uma linha em *Web.config* que diz ao ASP.net para usá-lo para [a maioria das operações de criptografia](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), incluindo autenticação de formulários, estado de exibição e chamadas para machineKey. Protect. A linha que é inserida é lida da seguinte maneira.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Você pode saber se o novo sistema de proteção de dados está ativo inspecionando campos como `__VIEWSTATE` , que devem começar com "CfDJ8" como no exemplo abaixo. "CfDJ8" é a representação base64 do cabeçalho mágico "09 F0 C9 F0" que identifica uma carga protegida pelo sistema de proteção de dados.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Configuração do pacote

O sistema de proteção de dados é instanciado com uma configuração padrão de configuração zero. No entanto, como as chaves padrão são mantidas no sistema de arquivos local, isso não funcionará para aplicativos que são implantados em um farm. Para resolver isso, você pode fornecer a configuração criando um tipo que subclasses DataProtectionStartup e substitui seu método configuraservices.

Abaixo está um exemplo de um tipo de inicialização de proteção de dados personalizado que configurou ambos os locais em que as chaves são mantidas e como elas são criptografadas em repouso. Ele também substitui a política de isolamento de aplicativo padrão fornecendo seu próprio nome de aplicativo.

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> Você também pode usar `<machineKey applicationName="my-app" ... />` o no lugar de uma chamada explícita para Setapplicationname. Esse é um mecanismo de conveniência para evitar forçar o desenvolvedor a criar um tipo derivado de DataProtectionStartup se tudo o que quisesse configurar estava definindo o nome do aplicativo.

Para habilitar essa configuração personalizada, volte para Web.config e procure o `<appSettings>` elemento que a instalação do pacote adicionou ao arquivo de configuração. Ele se parecerá com a seguinte marcação:

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

Preencha o valor em branco com o nome qualificado pelo assembly do tipo derivado de DataProtectionStartup que você acabou de criar. Se o nome do aplicativo for DataProtectionDemo, isso será semelhante ao mostrado abaixo.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

O sistema de proteção de dados recém-configurado agora está pronto para uso dentro do aplicativo.
