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
ms.openlocfilehash: bdb60608ba4bd99b24e458f0543b1f4226cadf44
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552176"
---
<span data-ttu-id="d4023-101">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="d4023-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="d4023-102">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="d4023-102">For authenticated users:</span></span>
  * <span data-ttu-id="d4023-103">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="d4023-103">Displays the current username.</span></span>
  * <span data-ttu-id="d4023-104">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d4023-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="d4023-105">Para usuários anônimos, o oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="d4023-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
