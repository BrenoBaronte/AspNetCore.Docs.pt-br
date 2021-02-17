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

<span data-ttu-id="e73f7-101">Esse comando gera os ativos do lado do cliente a serem atendidos ao executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e73f7-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="e73f7-102">Os ativos são colocados na pasta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e73f7-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="e73f7-103">O Webpack concluiu as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="e73f7-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="e73f7-104">Limpou os conteúdos do diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e73f7-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="e73f7-105">O TypeScript foi convertido em JavaScript em um processo conhecido como *transpilação*.</span><span class="sxs-lookup"><span data-stu-id="e73f7-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="e73f7-106">O JavaScript gerado foi desconfigurado para reduzir o tamanho do arquivo em um processo conhecido como *minificação*.</span><span class="sxs-lookup"><span data-stu-id="e73f7-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="e73f7-107">Copiou os arquivos JavaScript, CSS e HTML processados do *src* para o diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e73f7-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="e73f7-108">Injetou os seguintes elementos no arquivo *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="e73f7-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="e73f7-109">Uma `<link>` marca, referenciando o *wwwroot/Main. \<hash\> . arquivo CSS* .</span><span class="sxs-lookup"><span data-stu-id="e73f7-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="e73f7-110">Essa marca é colocada imediatamente antes do fim da marca `</head>`.</span><span class="sxs-lookup"><span data-stu-id="e73f7-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="e73f7-111">Uma `<script>` marca, referenciando reduzidos *wwwroot/Main. \<hash\> . arquivo js* .</span><span class="sxs-lookup"><span data-stu-id="e73f7-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="e73f7-112">Essa marca é colocada imediatamente antes do fim da marca `</body>`.</span><span class="sxs-lookup"><span data-stu-id="e73f7-112">This tag is placed immediately before the closing `</body>` tag.</span></span>