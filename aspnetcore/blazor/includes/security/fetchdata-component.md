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
ms.openlocfilehash: 8772f383a830936881564ca95a7f034ba08a5798
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552708"
---
O `FetchData` componente mostra como:

* Provisionar um token de acesso.
* Use o token de acesso para chamar uma API de recurso protegido no aplicativo do *servidor* .

A [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) diretiva indica para o Blazor WebAssembly sistema de autorização que o usuário deve ser autorizado para visitar esse componente. A presença do atributo no `Client` aplicativo não impede que a API no servidor seja chamada sem as credenciais apropriadas. O `Server` aplicativo também deve usar `[Authorize]` os pontos de extremidade apropriados para protegê-los corretamente.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> Cuida da solicitação de um token de acesso que pode ser adicionado à solicitação para chamar a API. Se o token for armazenado em cache ou o serviço for capaz de provisionar um novo token de acesso sem interação do usuário, a solicitação de token terá sucesso. Caso contrário, a solicitação de token falhará com um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , que é capturado em uma [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrução.

Para obter o token real a ser incluído na solicitação, o aplicativo deve verificar se a solicitação foi bem-sucedida chamando [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) .

Se a solicitação tiver sido bem-sucedida, a variável de token será populada com o token de acesso. A <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> Propriedade do token expõe a cadeia de caracteres literal a ser incluída no `Authorization` cabeçalho da solicitação.

Se a solicitação falhou porque o token não pôde ser provisionado sem interação do usuário, o resultado do token contém uma URL de redirecionamento. Navegar até essa URL leva o usuário para a página de logon e de volta para a página atual após uma autenticação bem-sucedida.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
