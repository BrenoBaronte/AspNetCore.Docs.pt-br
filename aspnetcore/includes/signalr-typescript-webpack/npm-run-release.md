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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552824"
---
```console
npm run release
```

Esse comando gera os ativos do lado do cliente a serem atendidos ao executar o aplicativo. Os ativos são colocados na pasta *wwwroot*.

O Webpack concluiu as seguintes tarefas:

* Limpou os conteúdos do diretório *wwwroot*.
* O TypeScript foi convertido em JavaScript em um processo conhecido como *transpilação*.
* O JavaScript gerado foi desconfigurado para reduzir o tamanho do arquivo em um processo conhecido como *minificação*.
* Copiou os arquivos JavaScript, CSS e HTML processados do *src* para o diretório *wwwroot*.
* Injetou os seguintes elementos no arquivo *wwwroot/index.html*:
  * Uma `<link>` marca, referenciando o *wwwroot/Main. \<hash\> . arquivo CSS* . Essa marca é colocada imediatamente antes do fim da marca `</head>`.
  * Uma `<script>` marca, referenciando reduzidos *wwwroot/Main. \<hash\> . arquivo js* . Essa marca é colocada imediatamente antes do fim da marca `</body>`.