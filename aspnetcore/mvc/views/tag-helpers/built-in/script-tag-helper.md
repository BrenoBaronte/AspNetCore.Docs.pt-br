---
title: Auxiliar de marca de script no ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra os atributos auxiliares de marca de script ASP.NET Core e a função que cada atributo desempenha ao estender o comportamento da marca de script HTML.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: b9b90c1c40fccbc7bb6b6c9050bd525b5fa8cd92
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407402"
---
# <a name="script-tag-helper-in-aspnet-core"></a>Auxiliar de marca de script no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) gera um link para um arquivo de script primário ou de retorno. Normalmente, o arquivo de script primário está em uma CDN ( [rede de distribuição de conteúdo](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) ).

[!INCLUDE[](~/includes/cdn.md)]

O auxiliar de marca de script permite que você especifique uma CDN para o arquivo de script e um fallback quando a CDN não estiver disponível. O auxiliar de marca de script fornece a vantagem de desempenho de uma CDN com a robustez da hospedagem local.

A marcação a seguir Razor mostra um `script` elemento com um fallback:

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

Não use o `<script>` atributo [Defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) do elemento para adiar o carregamento do script CDN. O auxiliar de marca de script processa o JavaScript que executa imediatamente a expressão [ASP-fallback-Test](#asp-fallback-test) . A expressão falhará se o carregamento do script CDN for adiado.

## <a name="commonly-used-script-tag-helper-attributes"></a>Atributos auxiliares de marca de script comumente usados

Consulte [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) para todos os atributos, propriedades e métodos auxiliares de marca de script.

### <a name="asp-fallback-test"></a>ASP – teste de fallback

O método de script definido no script primário a ser usado para o teste de fallback. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.

### <a name="asp-fallback-src"></a>ASP-fallback-src

A URL de uma marca de script para fazer fallback no caso do primário falhar. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
