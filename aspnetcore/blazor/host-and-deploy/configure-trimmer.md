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
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="cdc72-103">Configurar o corte para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cdc72-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="cdc72-104">Blazor WebAssembly executa a corte de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) para reduzir o tamanho da saída publicada.</span><span class="sxs-lookup"><span data-stu-id="cdc72-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="cdc72-105">Por padrão, o corte ocorre ao publicar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdc72-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="cdc72-106">A remoção pode ter efeitos prejudiciais.</span><span class="sxs-lookup"><span data-stu-id="cdc72-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="cdc72-107">Em aplicativos que usam reflexão, a recorte geralmente não pode determinar os tipos necessários para reflexão em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="cdc72-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="cdc72-108">Para cortar aplicativos que usam reflexão, o corte deve ser informado sobre os tipos necessários para reflexão no código do aplicativo e nos pacotes ou estruturas dos quais o aplicativo depende.</span><span class="sxs-lookup"><span data-stu-id="cdc72-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="cdc72-109">O corte também não é capaz de reagir ao comportamento dinâmico de um aplicativo no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="cdc72-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="cdc72-110">Para garantir que o aplicativo cortado funcione corretamente depois de implantado, teste a saída publicada com frequência ao desenvolver.</span><span class="sxs-lookup"><span data-stu-id="cdc72-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="cdc72-111">Para configurar o corte, consulte o artigo [Opções de corte](/dotnet/core/deploying/trimming-options) na documentação dos conceitos básicos do .net, que inclui orientação sobre os seguintes assuntos:</span><span class="sxs-lookup"><span data-stu-id="cdc72-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="cdc72-112">Desabilite a corte para todo o aplicativo com a `<PublishTrimmed>` propriedade no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="cdc72-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="cdc72-113">Controle como o IL não utilizado de forma agressiva é Descartado pelo corte.</span><span class="sxs-lookup"><span data-stu-id="cdc72-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="cdc72-114">Pare o corte da corte de assemblies específicos.</span><span class="sxs-lookup"><span data-stu-id="cdc72-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="cdc72-115">Assemblies "raiz" para corte.</span><span class="sxs-lookup"><span data-stu-id="cdc72-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="cdc72-116">Avisos de superfície para tipos refletidos definindo a `<SuppressTrimAnalysisWarnings>` propriedade como `false` no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="cdc72-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="cdc72-117">Corte de símbolo de controle e suporte degugger.</span><span class="sxs-lookup"><span data-stu-id="cdc72-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="cdc72-118">Definir recursos de corte para cortar recursos da biblioteca da estrutura.</span><span class="sxs-lookup"><span data-stu-id="cdc72-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cdc72-119">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cdc72-119">Additional resources</span></span>

* [<span data-ttu-id="cdc72-120">Cortar implantações e executáveis autossuficientes</span><span class="sxs-lookup"><span data-stu-id="cdc72-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
