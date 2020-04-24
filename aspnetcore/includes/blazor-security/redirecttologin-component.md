<span data-ttu-id="70d84-101">O `RedirectToLogin` componente (*compartilhado/RedirectToLogin. Razor*):</span><span class="sxs-lookup"><span data-stu-id="70d84-101">The `RedirectToLogin` component (*Shared/RedirectToLogin.razor*):</span></span>

* <span data-ttu-id="70d84-102">Gerencia o redirecionamento de usuários não autorizados para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="70d84-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="70d84-103">Preserva a URL atual que o usuário está tentando acessar para que possa ser retornada a essa página se a autenticação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="70d84-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
