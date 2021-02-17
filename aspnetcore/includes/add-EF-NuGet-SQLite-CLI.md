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
ms.openlocfilehash: 1a9e98a84093f89f3859454f482dfe59c8504d9b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551453"
---
<span data-ttu-id="cf6ed-101">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="cf6ed-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

<span data-ttu-id="cf6ed-102">Os comandos anteriores adicionam:</span><span class="sxs-lookup"><span data-stu-id="cf6ed-102">The preceding commands add:</span></span>

* <span data-ttu-id="cf6ed-103">A [ferramenta ASPNET-CodeGenerator scaffolding](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="cf6ed-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="cf6ed-104">As ferramentas de Entity Framework Core para o CLI do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cf6ed-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="cf6ed-105">O provedor do EF Core SQLite, que instala o pacote EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="cf6ed-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="cf6ed-106">Pacotes necessários para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` e `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="cf6ed-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="cf6ed-107">Para obter orientação sobre a configuração de vários ambientes que permite que um aplicativo Configure seus contextos de banco de dados por ambiente, consulte <xref:fundamentals/environments#environment-based-startup-class-and-methods> .</span><span class="sxs-lookup"><span data-stu-id="cf6ed-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]