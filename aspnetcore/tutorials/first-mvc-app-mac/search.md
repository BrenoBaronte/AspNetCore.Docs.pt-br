---
title: Adicionando uma pesquisa a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Mostra como adicionar uma pesquisa a um aplicativo ASP.NET Core MVC simples
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: d69e5529-ffff-4628-855d-200202096269
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/search
ms.openlocfilehash: 8dab5293ab6a6fe65288bb230e4f39af462ba28b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/search1.md)]

Observação: o SQLlite diferencia maiúsculas de minúsculas e, portanto, você precisará pesquisar “Ghost” e não “ghost”.

[!INCLUDE[adding-model](../../includes/mvc-intro/search2.md)]

Altere a marcação `<form>` na exibição *Views\movie\Index.cshtml* do Razor para especificar `method="get"`:

```html
<form asp-controller="Movies" asp-action="Index" method="get">
```

[!INCLUDE[adding-model](../../includes/mvc-intro/search3.md)]

>[!div class="step-by-step"]
[Anterior – Exibições e métodos do controlador](controller-methods-views.md)
[Próximo – Adicionar um campo](new-field.md)
