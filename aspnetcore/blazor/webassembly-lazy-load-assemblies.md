---
title: Assemblies de carga lenta no ASP.NET Core Blazor WebAssembly
author: guardrex
description: Descubra como carregar com lentas os assemblies em ASP.NET Core Blazor WebAssembly aplicativos.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6e7fa6e231e97793fbf7e1ac1d208bf3013c6fce
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506560"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>Assemblies de carga lenta no ASP.NET Core Blazor WebAssembly

Por [Safia Abdalla](https://safia.rocks) e [Luke Latham](https://github.com/guardrex)

Blazor WebAssembly o desempenho de inicialização do aplicativo pode ser melhorado ao adiar o carregamento de alguns assemblies de aplicativo até que eles sejam necessários, o que é chamado de *carregamento lento*. Por exemplo, os assemblies que são usados somente para processar um único componente podem ser configurados para carregar somente se o usuário navegar para esse componente. Após o carregamento, os assemblies são armazenados em cache no lado do cliente e estão disponíveis para todas as navegações futuras.

Blazoro recurso de carregamento lento do permite que você marque os assemblies de aplicativo para carregamento lento, que carrega os assemblies durante o tempo de execução quando o usuário navega para uma rota específica. O recurso consiste em alterações no arquivo do projeto e alterações no roteador do aplicativo.

> [!NOTE]
> O carregamento lento do assembly não beneficia Blazor Server aplicativos porque assemblies não são baixados para o cliente em um Blazor Server aplicativo.

## <a name="project-file"></a>Arquivo de projeto

Marque assemblies para carregamento lento no arquivo de projeto do aplicativo ( `.csproj` ) usando o `BlazorWebAssemblyLazyLoad` Item. Use o nome do assembly com a `.dll` extensão. A Blazor estrutura impede que os assemblies especificados por esse grupo de itens sejam carregados na inicialização do aplicativo. O exemplo a seguir marca um assembly personalizado grande ( `GrantImaharaRobotControls.dll` ) para carregamento lento. Se um assembly marcado para carregamento lento tiver dependências, eles também deverão ser marcados para carregamento lento no arquivo de projeto.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a>componente `Router`

Blazor`Router`o componente do designa quais assemblies Blazor pesquisam componentes roteáveis. O `Router` componente também é responsável por renderizar o componente para a rota onde o usuário navega. O `Router` componente dá suporte a um `OnNavigateAsync` recurso que pode ser usado em conjunto com o carregamento lento.

No componente do aplicativo `Router` ( `App.razor` ):

* Adicione um `OnNavigateAsync` retorno de chamada. O `OnNavigateAsync` manipulador é invocado quando o usuário:
  * Visita uma rota pela primeira vez navegando diretamente de seu navegador.
  * Navega para uma nova rota usando um link ou uma <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocação.
* Se os assemblies com carregamento lento contiverem componentes roteáveis, adicione uma [lista](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (por exemplo, nomeada `lazyLoadedAssemblies` ) ao componente. Os assemblies são passados de volta para a <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> coleção caso os assemblies contenham componentes roteáveis. A estrutura pesquisa os assemblies em busca de rotas e atualiza a coleção de rotas se alguma nova rota for encontrada.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Se o `OnNavigateAsync` retorno de chamada lançar uma exceção sem tratamento, a [ Blazor interface do usuário de erro](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) será invocada.

### <a name="assembly-load-logic-in-onnavigateasync"></a>Lógica de carga de assembly em `OnNavigateAsync`

`OnNavigateAsync` tem um `NavigationContext` parâmetro que fornece informações sobre o evento de navegação assíncrona atual, incluindo o caminho de destino ( `Path` ) e o token de cancelamento ( `CancellationToken` ):

* A `Path` propriedade é o caminho de destino do usuário em relação ao caminho base do aplicativo, como `/robot` .
* O `CancellationToken` pode ser usado para observar o cancelamento da tarefa assíncrona. `OnNavigateAsync` Cancela automaticamente a tarefa de navegação em execução no momento quando o usuário navega para uma página diferente.

No `OnNavigateAsync` , implemente a lógica para determinar os assemblies a serem carregados. As opções incluem:

* Verificações condicionais dentro do `OnNavigateAsync` método.
* Uma tabela de pesquisa que mapeia rotas para nomes de assembly, injetada no componente ou implementada dentro do [`@code`](xref:mvc/views/razor#code) bloco.

`LazyAssemblyLoader` é um serviço singleton fornecido pela estrutura para carregar assemblies. Injetar `LazyAssemblyLoader` no `Router` componente:

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

O `LazyAssemblyLoader` fornece o `LoadAssembliesAsync` método que:

* Usa a interoperabilidade JS para buscar assemblies por meio de uma chamada de rede.
* Carrega assemblies no tempo de execução em execução no Webassembly no navegador.

A implementação de carregamento lento da estrutura dá suporte ao carregamento lento com o pré-processamento em uma solução hospedada Blazor . Durante o pré-processamento, todos os assemblies, incluindo os marcados para carregamento lento, são considerados carregados. Registrar manualmente `LazyAssemblyLoader` no método do projeto do *servidor* `Startup.ConfigureServices` ( `Startup.cs` ):

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a>Interação do usuário com `<Navigating>` conteúdo

Durante o carregamento de assemblies, que pode levar vários segundos, o `Router` componente pode indicar ao usuário que uma transição de página está ocorrendo:

* Adicione uma [`@using`](xref:mvc/views/razor#using) diretiva para o <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.
* Adicione uma `<Navigating>` marca ao componente com marcação a ser exibida durante eventos de transição de página.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

### <a name="handle-cancellations-in-onnavigateasync"></a>Manipular cancelamentos em `OnNavigateAsync`

O `NavigationContext` objeto passado para o `OnNavigateAsync` retorno de chamada contém um `CancellationToken` que é definido quando um novo evento de navegação ocorre. O `OnNavigateAsync` retorno de chamada deve gerar quando esse token de cancelamento é definido para evitar continuar a executar o `OnNavigateAsync` retorno de chamada em uma navegação desatualizada.

Se um usuário navegar para rotear a e, em seguida, imediatamente para a rota B, o aplicativo não deverá continuar executando o `OnNavigateAsync` retorno de chamada para a rota a:

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

> [!NOTE]
> Não lançar se o token de cancelamento no `NavigationContext` for cancelado pode resultar em um comportamento indesejado, como a renderização de um componente de uma navegação anterior.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>`OnNavigateAsync` eventos e arquivos de assembly renomeados

O carregador de recursos depende dos nomes de assembly definidos no `blazor.boot.json` arquivo. Se os [assemblies forem renomeados](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), os nomes de assembly usados em `OnNavigateAsync` métodos e os nomes de assembly no `blazor.boot.json` arquivo ficarão fora de sincronia.

Para corrigir isso:

* Verifique se o aplicativo está em execução no ambiente de produção ao determinar quais nomes de assembly usar.
* Armazene os nomes de assembly renomeados em um arquivo separado e leia esse arquivo para determinar qual nome de assembly usar nos `LazyLoadAssemblyService` `OnNavigateAsync` métodos e.

### <a name="complete-example"></a>Exemplo completo

O componente completo a seguir `Router` demonstra como carregar o `GrantImaharaRobotControls.dll` assembly quando o usuário navega para `/robot` . Durante as transições de página, uma mensagem com estilo é exibida para o usuário.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="troubleshoot"></a>Solucionar problemas

* Se ocorrer uma renderização inesperada (por exemplo, um componente de uma navegação anterior é renderizado), confirme se o código será gerado se o token de cancelamento for definido.
* Se os assemblies ainda estiverem carregados no início do aplicativo, verifique se o assembly está marcado como carregado como lento no arquivo de projeto.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/webassembly-performance-best-practices>
