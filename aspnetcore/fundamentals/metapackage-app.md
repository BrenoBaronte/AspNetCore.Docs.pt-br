---
title: Metapacote Microsoft.AspNetCore.App para ASP.NET Core
author: Rick-Anderson
description: A estrutura compartilhada do Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511373"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft.AspNetCore.App para ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 O framework compartilhado ASP.NET`Microsoft.AspNetCore.App`Core ( ) contém conjuntos que são desenvolvidos e suportados pela Microsoft. `Microsoft.AspNetCore.App`é instalado quando o [.NET Core 3.0 ou posterior SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) é instalado. A *estrutura compartilhada* é o conjunto de conjuntos *(arquivos .dll)* que são instalados na máquina e inclui um componente de tempo de execução e um pacote de segmentação. Saiba mais em [A estrutura compartilhada](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* Projetos que `Microsoft.NET.Sdk.Web` visam o SDK `Microsoft.AspNetCore.App` fazem referência implícita à estrutura.

Não são necessárias referências adicionais para esses projetos:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

A estrutura compartilhada do núcleo ASP.NET:

* Não inclui dependências de terceiros.
* Inclui todos os pacotes suportados pela equipe do ASP.NET Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este recurso exige o ASP.NET Core 2.x direcionado ao .NET Core 2.x.

O [metapacote](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) para ASP.NET Core:

* Não tem dependências de terceiros, com exceção de [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) e [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Essas dependências de terceiros são consideradas necessárias para garantir o funcionamento dos principais recursos das estruturas.
* Inclui todos os pacotes com suporte pela equipe do ASP.NET Core, exceto aqueles que contêm dependências de terceiros (que não sejam aqueles mencionados anteriormente).
* Inclui todos os pacotes com suporte pela equipe do Entity Framework Core, exceto aqueles que contêm dependências de terceiros (que não sejam aqueles mencionados anteriormente).

Todos os recursos do ASP.NET Core 2.x e do Entity Framework Core 2.x são incluídos no pacote `Microsoft.AspNetCore.App`. Os modelos de projeto padrão direcionados ao ASP.NET Core 2.x usam este pacote. Recomendamos que os aplicativos direcionados ASP.NET Core 2.x `Microsoft.AspNetCore.App` e Entity Framework Core 2.x usem o pacote.

O número de versão do metapacote `Microsoft.AspNetCore.App` representa a versão mínima do ASP.NET Core e a versão do Entity Framework Core.

O uso do metapacote `Microsoft.AspNetCore.App` fornece restrições de versões que protegem seu aplicativo:

* Se um pacote incluído tem uma dependência (não direta) transitiva em um pacote no `Microsoft.AspNetCore.App`, e os números de versão forem diferentes, o NuGet gera um erro.
* Outros pacotes adicionados ao seu aplicativo não podem alterar a versão dos pacotes incluídos no `Microsoft.AspNetCore.App`.
* A consistência de versão garante uma experiência confiável. `Microsoft.AspNetCore.App` foi projetado para evitar combinações de versão não testado de bits relacionados que estão sendo usados juntos no mesmo aplicativo.

Aplicativos que usam o metapacote `Microsoft.AspNetCore.App` aproveitam automaticamente a estrutura compartilhada do ASP.NET Core. Quando você usa o metapacote `Microsoft.AspNetCore.App`, **** nenhum&mdash; ativo dos pacotes NuGet do ASP.NET Core referenciados é implantado com o aplicativo, porque a estrutura compartilhada do ASP.NET Core contém esses ativos. Os ativos na estrutura compartilhada são pré-compilados para melhorar o tempo de inicialização do aplicativo. Saiba mais em [A estrutura compartilhada](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

O arquivo de projeto `Microsoft.AspNetCore.App` a seguir faz referência ao metapacote para ASP.NET Core e representa um modelo típico do ASP.NET Core 2.2:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

A marcação anterior representa um modelo típico do ASP.NET Core 2.x. Ela não especifica um número de versão para a referência de pacote `Microsoft.AspNetCore.App`. Quando a versão não for especificada, uma versão [implícita](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) será especificada pelo SDK, ou seja, `Microsoft.NET.Sdk.Web`. Recomendamos que você conte com a versão implícita especificada pelo SDK, e não defina explicitamente o número de versão na referência de pacote. Se tiver dúvidas sobre essa abordagem, deixe um comentário do GitHub na [Discussão para a versão implícita do Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

A versão implícita é definida como `major.minor.0` para aplicativos portátil. O mecanismo de roll forward estrutura compartilhada executará o aplicativo na versão compatível mais recente entre as estruturas compartilhadas instaladas. Para garantir que a mesma versão seja usada no desenvolvimento, no teste e na produção, certifique-se de que a mesma versão da estrutura compartilhada seja instalada em todos os ambientes. Para aplicativos independentes, o número de versão implícita é definido como `major.minor.patch` da estrutura compartilhada incluída no SDK instalado.

Especificar um número de versão na referência `Microsoft.AspNetCore.App`**não** garante que a versão da estrutura compartilhada será escolhida. Por exemplo, suponha que a versão "2.2.1" seja especificada, mas "2.2.3" está instalado. Nesse caso, o aplicativo usará "2.2.3". Embora não seja recomendado, você pode desabilitar o roll forward (patch e/ou secundária). Para obter mais informações sobre como efetuar roll forward do host dotnet e como configurar seu comportamento, veja [Efetuar roll forward do host dotnet](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk` deve ser definido como `Microsoft.NET.Sdk.Web` para usar o `Microsoft.AspNetCore.App` da versão implícita. Quando `<Project Sdk="Microsoft.NET.Sdk">` (sem o `.Web` à direita) é usado:

* O aviso a seguir é gerado:

  *Aviso NU1604: Dependência do projeto Microsoft.AspNetCore.App não contém um limite inferior inclusivo. Inclua um limite inferior na versão de dependência para garantir resultados consistentes de restauração.*

* Esse é um problema conhecido do SDK do .NET Core 2.1.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Atualizar o ASP.NET Core

O `Microsoft.AspNetCore.App` [metapacote](/dotnet/core/packages#metapackages) não é um pacote tradicional atualizado do NuGet. Semelhante ao `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` representa um runtime compartilhado, que tem semântica de controle de versão especial tratada fora do NuGet. Para obter mais informações, veja [Pacotes, metapacotes e estruturas](/dotnet/core/packages).

Para atualizar o ASP.NET Core:

* Em computadores de desenvolvimento e servidores de compilação: baixe e instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).
* Nos servidores de implantação: baixe e instale o [runtime do .NET Core](https://dotnet.microsoft.com/download).

 Os aplicativos efetuarão roll forward para a versão mais recente instalada na reinicialização do aplicativo. Não é necessário atualizar o número de versão `Microsoft.AspNetCore.App` no arquivo de projeto. Para obter mais informações, consulte [Roll forward de aplicativos dependentes de estrutura](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Se seu aplicativo tiver usado `Microsoft.AspNetCore.All`, veja [Migração do Microsoft.AspNetCore.All para Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).

::: moniker-end
