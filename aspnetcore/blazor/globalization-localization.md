---
title: ASP.NET Core Blazor globalização e localização
author: guardrex
description: Saiba como tornar Razor os componentes acessíveis para os usuários em várias culturas e linguagens.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
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
uid: blazor/globalization-localization
ms.openlocfilehash: 4345dd8525c2e72aaddc8e45a4fd4d9bfdd63040
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326524"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor globalização e localização

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razor os componentes podem se tornar acessíveis para os usuários em várias culturas e linguagens. Os seguintes cenários de globalização e localização do .NET estão disponíveis:

* . Sistema de recursos da rede
* Formatação de número e data específicos da cultura

No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> e <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *têm suporte* em Blazor aplicativos.
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> , e a localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em Blazor aplicativos.

Para obter mais informações, consulte <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalização

Blazor[`@bind`](xref:mvc/views/razor#bind)a funcionalidade do executa formatos e analisa valores para exibição com base na cultura atual do usuário.

A cultura atual pode ser acessada a partir da <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> propriedade.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> é usado para os seguintes tipos de campo ( `<input type="{TYPE}" />` ):

* `date`
* `number`

Os tipos de campo anteriores:

* São exibidos usando suas regras de formatação baseadas em navegador apropriadas.
* Não pode conter texto de forma livre.
* Fornecer características de interação do usuário com base na implementação do navegador.

Os seguintes tipos de campo têm requisitos de formatação específicos e não têm suporte no momento Blazor porque não têm suporte de todos os principais navegadores:

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind) dá suporte ao `@bind:culture` parâmetro para fornecer um <xref:System.Globalization.CultureInfo?displayProperty=fullName> para análise e formatação de um valor. Não é recomendável especificar uma cultura ao usar os `date` `number` tipos de campo e. `date` e `number` têm Blazor suporte interno que fornece a cultura necessária.

## <a name="localization"></a>Localização

### Blazor WebAssembly

Blazor WebAssembly os aplicativos definem a cultura usando a [preferência de idioma](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)do usuário.

Para configurar explicitamente a cultura, defina <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> e <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> em `Program.Main` .

::: moniker range=">= aspnetcore-5.0"

Por padrão, Blazor WebAssembly o carrega os recursos mínimos de globalização necessários para exibir valores, como datas e moedas, na cultura do usuário. Os aplicativos que devem oferecer suporte à alteração dinâmica da cultura devem ser configurados `BlazorWebAssemblyLoadAllGlobalizationData` no arquivo do projeto:

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

Blazor WebAssembly também pode ser configurado para iniciar usando uma cultura de aplicativo específica usando as opções passadas para o `Blazor.start` . Por exemplo, o exemplo a seguir mostra um aplicativo configurado para ser iniciado usando a `en-GB` cultura:

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

O valor de `applicationCulture` deve estar em conformidade com o [formato de marca de idioma BCP-47](https://tools.ietf.org/html/bcp47).

Se o aplicativo não exigir localização, você poderá configurar o aplicativo para dar suporte à cultura invariável, que se baseia na `en-US` cultura:

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Por padrão, a configuração do vinculador de IL (linguagem intermediária) para Blazor WebAssembly aplicativos retira informações de internacionalização, exceto as localidades explicitamente solicitadas. Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.

::: moniker-end

Embora a cultura que Blazor seleciona por padrão possa ser suficiente para a maioria dos usuários, considere a possibilidade de oferecer uma maneira para os usuários especificarem sua localidade preferida. Para um Blazor WebAssembly aplicativo de exemplo com um seletor de cultura, consulte o [`LocSample`](https://github.com/pranavkm/LocSample) aplicativo de exemplo de localização.

### Blazor Server

Blazor Server os aplicativos são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware). O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.

A cultura pode ser definida usando uma das seguintes abordagens:

* [Cookie&](#cookies)
* [Fornecer interface do usuário para escolher a cultura](#provide-ui-to-choose-the-culture)

Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.

#### <a name="no-loccookies"></a>Cookies

Uma cultura de localização cookie pode persistir a cultura do usuário. O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário. 

O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura. Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura. Portanto, o uso de uma cultura de localização cookie é a abordagem recomendada.

Qualquer técnica pode ser usada para atribuir uma cultura se a cultura for persistida em uma localização cookie . Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina a cultura de localização cookie no esquema do aplicativo.

O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lida pelo middleware de localização. Crie uma Razor expressão no `Pages/_Host.cshtml` arquivo imediatamente dentro da marca de abertura `<body>` :

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

A localização é manipulada pelo aplicativo na seguinte sequência de eventos:

1. O navegador envia uma solicitação HTTP inicial para o aplicativo.
1. A cultura é atribuída pelo middleware de localização.
1. A Razor expressão na `_Host` página ( `_Host.cshtml` ) persiste a cultura em um cookie como parte da resposta.
1. O navegador abre uma conexão WebSocket para criar uma Blazor Server sessão interativa.
1. O middleware de localização lê o cookie e atribui a cultura.
1. A Blazor Server sessão começa com a cultura correta.

#### <a name="provide-ui-to-choose-the-culture"></a>Fornecer interface do usuário para escolher a cultura

Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* . O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso seguro. O usuário é redirecionado para uma página de entrada e Redirecionado de volta para o recurso original. 

O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador. O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.

Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Use o <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> resultado da ação para evitar ataques de redirecionamento abertos. Para obter mais informações, consulte <xref:security/preventing-open-redirects>.

Se o aplicativo não estiver configurado para processar ações do controlador:

* Adicionar serviços MVC à coleção de serviços em `Startup.ConfigureServices` :

  ```csharp
  services.AddControllers();
  ```

* Adicionar roteamento de ponto de extremidade do controlador em `Startup.Configure` :

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/localization>
