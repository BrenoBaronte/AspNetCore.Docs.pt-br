---
title: Configurar o corte para ASP.NET Core Blazor
author: guardrex
description: Saiba como controlar o vinculador (corte) de IL (linguagem intermediária) ao criar um Blazor aplicativo.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975204"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>Configurar o corte para ASP.NET Core Blazor

Blazor WebAssembly executa a corte de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) para reduzir o tamanho da saída publicada. Por padrão, o corte ocorre ao publicar um aplicativo.

A remoção pode ter efeitos prejudiciais. Em aplicativos que usam reflexão, a recorte geralmente não pode determinar os tipos necessários para reflexão em tempo de execução. Para cortar aplicativos que usam reflexão, o corte deve ser informado sobre os tipos necessários para reflexão no código do aplicativo e nos pacotes ou estruturas dos quais o aplicativo depende. O corte também não é capaz de reagir ao comportamento dinâmico de um aplicativo no tempo de execução. Para garantir que o aplicativo cortado funcione corretamente depois de implantado, teste a saída publicada com frequência ao desenvolver.

Para configurar o corte, consulte o artigo [Opções de corte](/dotnet/core/deploying/trimming-options) na documentação dos conceitos básicos do .net, que inclui orientação sobre os seguintes assuntos:

* Desabilite a corte para todo o aplicativo com a `<PublishTrimmed>` propriedade no arquivo de projeto.
* Controle como o IL não utilizado de forma agressiva é Descartado pelo corte.
* Pare o corte da corte de assemblies específicos.
* Assemblies "raiz" para corte.
* Avisos de superfície para tipos refletidos definindo a `<SuppressTrimAnalysisWarnings>` propriedade como `false` no arquivo de projeto.
* Corte de símbolo de controle e suporte degugger.
* Definir recursos de corte para cortar recursos da biblioteca da estrutura.

## <a name="additional-resources"></a>Recursos adicionais

* [Cortar implantações e executáveis autossuficientes](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
