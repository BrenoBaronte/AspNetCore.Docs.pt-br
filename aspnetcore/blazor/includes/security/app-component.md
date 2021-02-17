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
ms.openlocfilehash: d632ab0604f81f7b6067d4535b0f5da0afe2e0ad
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551594"
---
O `App` componente ( `App.razor` ) é semelhante ao `App` componente encontrado em Blazor Server aplicativos:

* O <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> componente gerencia a exposição do <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> ao restante do aplicativo.
* O <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente garante que o usuário atual esteja autorizado a acessar uma determinada página ou, de outra forma, renderiza o `RedirectToLogin` componente.
* O `RedirectToLogin` componente gerencia o redirecionamento de usuários não autorizados para a página de logon.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!include[](../prefer-exact-matches.md)]
