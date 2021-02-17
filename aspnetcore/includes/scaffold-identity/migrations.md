---
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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551364"
---
O Identity código de banco de dados gerado requer [migrações Entity Framework Core](/ef/core/managing-schemas/migrations/). Crie uma migração e atualize o banco de dados. Por exemplo, execute os seguintes comandos:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

No **console do Gerenciador de pacotes** do Visual Studio:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

O parâmetro de Identity nome "criar esquema" para o `Add-Migration` comando é arbitrário. `"CreateIdentitySchema"` Descreve a migração.
