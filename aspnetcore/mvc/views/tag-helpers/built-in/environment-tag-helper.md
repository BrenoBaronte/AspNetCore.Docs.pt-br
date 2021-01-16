---
title: Auxiliar de Marca de Ambiente no ASP.NET Core
author: pkellner
description: Definição de Auxiliar de Marca de Ambiente do ASP.NET Core, incluindo todas as propriedades
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: d63364b0c052ba7f9e745e1ad829b8d1ca9122d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253118"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Auxiliar de Marca de Ambiente no ASP.NET Core

Por [Peter Kellner](https://peterkellner.net) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)

A marca de ambiente auxiliar processa condicionalmente seu conteúdo embutido com base no [ambiente de hospedagem](xref:fundamentals/environments)atual. Atributo único do Auxiliar de Marca de Ambiente, `names`, é uma lista separada por vírgulas de nomes de ambiente. Se nenhum dos nomes de ambiente fornecido corresponder ao ambiente atual, o conteúdo contido será renderizado.

Para obter uma visão geral de Auxiliares de marcação, consulte <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Atributos do Auxiliar de Marca de Ambiente

### <a name="names"></a>nomes

`names` aceita um único nome de ambiente de hospedagem ou uma lista separada por vírgula de nomes de ambiente de hospedagem que disparam a renderização do conteúdo contido.

Os valores de ambiente são comparados ao valor atual retornado por [IWebHostEnvironment. environmentname](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). A comparação ignora o uso de maiúsculas.

O exemplo a seguir usa um Auxiliar de Marca de Ambiente. O conteúdo será renderizado se o ambiente de hospedagem for De Preparo ou de Produção:

```cshtml
<environment names="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>incluir e excluir atributos

`include`o & `exclude` controle de atributos que renderiza o conteúdo incluído com base nos nomes de ambiente de hospedagem incluídos ou excluídos.

### <a name="include"></a>include

A propriedade `include` exibe um comportamento semelhante para o atributo `names`. Um ambiente listado no `include` valor do atributo deve corresponder ao ambiente de hospedagem do aplicativo ([IWebHostEnvironment. environmentname](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) para renderizar o conteúdo da `<environment>` marca.

```cshtml
<environment include="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>excluir

Em contraste com o atributo `include`, o conteúdo da marcação `<environment>` é processado quando o ambiente de hospedagem não corresponde a um ambiente listado no valor do atributo `exclude`.

```cshtml
<environment exclude="Development">
    <strong>IWebHostEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/environments>
