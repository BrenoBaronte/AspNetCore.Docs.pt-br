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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552797"
---
Blazor aplicativos de servidor Live na memória do servidor. Isso significa que há vários aplicativos hospedados no mesmo processo. Para cada sessão de aplicativo, Blazor inicia um circuito com seu próprio escopo de contêiner de di. Isso significa que os serviços com escopo são exclusivos por Blazor sessão.

> [!WARNING]
> Não recomendamos aplicativos no mesmo estado de compartilhamento do servidor usando serviços singleton, a menos que seja feito um tratamento extremo, pois isso pode introduzir vulnerabilidades de segurança, como o vazamento do estado do usuário entre circuitos.

Você pode usar serviços singleton com estado em Blazor aplicativos se eles forem especificamente projetados para ele. Por exemplo, é possível usar um cache de memória como um singleton porque ele requer uma chave para acessar uma determinada entrada, supondo que os usuários não tenham controle de quais chaves de cache são usadas.

**Além disso, por motivos de segurança, você não deve usar <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> em Blazor aplicativos.** Blazor os aplicativos são executados fora do contexto do pipeline de ASP.NET Core. Não há garantia de que <xref:Microsoft.AspNetCore.Http.HttpContext> esteja disponível dentro do <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , nem é garantido que esteja segurando o contexto que iniciou o Blazor aplicativo.

A maneira recomendada de passar o estado de solicitação para o Blazor aplicativo é por meio de parâmetros para o componente raiz na renderização inicial do aplicativo:

* Defina uma classe com todos os dados que você deseja passar para o Blazor aplicativo.
* Preencha esses dados da Razor página usando o <xref:Microsoft.AspNetCore.Http.HttpContext> disponível naquele momento.
* Passe os dados para o Blazor aplicativo como um parâmetro para o componente raiz (aplicativo).
* Defina um parâmetro no componente raiz para manter os dados que estão sendo passados para o aplicativo.
* Usar os dados específicos do usuário dentro do aplicativo; ou, como alternativa, copie esses dados em um serviço com escopo dentro <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> para que ele possa ser usado em todo o aplicativo.

Para obter mais informações e um código de exemplo, consulte <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.
