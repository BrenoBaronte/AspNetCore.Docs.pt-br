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
ms.openlocfilehash: 27cb64dc8734fcb6d165795515096c9d9dd2a9cc
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551697"
---
<span data-ttu-id="f5b31-101">Execute os seguintes comandos da CLI do .NET:</span><span class="sxs-lookup"><span data-stu-id="f5b31-101">Run the following .NET CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="f5b31-102">Os comandos anteriores adicionam:</span><span class="sxs-lookup"><span data-stu-id="f5b31-102">The preceding commands add:</span></span>

* <span data-ttu-id="f5b31-103">A [ferramenta ASPNET-CodeGenerator scaffolding](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="f5b31-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="f5b31-104">As ferramentas de EF Core para a CLI do .NET.</span><span class="sxs-lookup"><span data-stu-id="f5b31-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="f5b31-105">O provedor do EF Core SQLite, que instala o pacote EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="f5b31-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="f5b31-106">Pacotes necessários para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` e `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="f5b31-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="f5b31-107">Para obter orientação sobre a configuração de vários ambientes que permite que um aplicativo Configure seus contextos de banco de dados por ambiente, consulte <xref:fundamentals/environments#environment-based-startup-class-and-methods> .</span><span class="sxs-lookup"><span data-stu-id="f5b31-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
