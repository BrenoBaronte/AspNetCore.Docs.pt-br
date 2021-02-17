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
ms.openlocfilehash: f0d34557b5442f41ebd1ef9cb3a61122b8516f7d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551861"
---
<span data-ttu-id="85834-101">Se você receber um erro scaffolding, verifique se o moniker da estrutura de destino (TFM) corresponde à versão do pacote NuGet no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="85834-101">If you get a scaffolding error, verify the Target Framework Moniker (TFM) matches the NuGet package version in the project file.</span></span> <span data-ttu-id="85834-102">Por exemplo, o arquivo de projeto a seguir usa a versão 5,0 para .NET e os pacotes NuGet listados:</span><span class="sxs-lookup"><span data-stu-id="85834-102">For example, the following project file uses version 5.0 for .NET and the listed NuGet packages:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.UI" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="5.0.0-*" />
  </ItemGroup>

</Project>
```
