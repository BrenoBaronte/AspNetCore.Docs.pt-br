---
title: BlazorVirtualização de componente ASP.NET Core
author: guardrex
description: Saiba como usar a virtualização de componentes em Blazor aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 706564bb8607d0bb25c092c31a72e5790c825ee4
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024672"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>BlazorVirtualização de componente ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura. A virtualização é uma técnica para limitar a renderização da interface do usuário apenas às partes visíveis no momento. Por exemplo, a virtualização é útil quando o aplicativo deve renderizar uma longa lista de itens e apenas um subconjunto de itens é necessário para ser visível em um determinado momento. Blazorfornece o [ `Virtualize` componente](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) que pode ser usado para adicionar a virtualização aos componentes de um aplicativo.

O `Virtualize` componente pode ser usado quando:

* Renderizando um conjunto de itens de dados em um loop.
* A maioria dos itens não está visível devido à rolagem.
* Os itens processados são exatamente do mesmo tamanho. Quando o usuário rola para um ponto arbitrário, o componente pode calcular os itens visíveis a serem mostrados.

Sem a virtualização, uma lista típica pode usar um [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop C# para renderizar cada item na lista:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

Se a lista contiver milhares de itens, a renderização da lista poderá levar muito tempo. O usuário pode experimentar um atraso de interface do usuário perceptível.

Em vez de renderizar cada item na lista todos de uma vez, substitua o [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop pelo `Virtualize` componente e especifique uma fonte de item fixa com <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> . Somente os itens visíveis no momento são renderizados:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

Se não especificar um contexto para o componente com `Context` , use o `context` valor ( `context.{PROPERTY}` / `@context.{PROPERTY}` ) no modelo de conteúdo do item:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> O processo de mapeamento de objetos de modelo para elementos e componentes pode ser controlado com o `@key` atributo de diretiva [] [xref: MVC/views/Razor # key]. `@key` faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave.
>
> Para obter mais informações, consulte os seguintes artigos:
>
> <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> <xref:mvc/views/razor#key>

O `Virtualize` componente:

* Calcula quantos itens processar com base na altura do contêiner e no tamanho dos itens renderizados.
* Recalcula e rerenderiza os itens à medida que o usuário rola.
* Busca apenas a fatia de registros de uma API externa que corresponda à região visível atual, em vez de baixar todos os dados da coleção.

O conteúdo do item para o `Virtualize` componente pode incluir:

* HTML e código sem formatação Razor , como mostra o exemplo anterior.
* Um ou mais Razor componentes.
* Uma combinação de HTML/ Razor e Razor componentes.

## <a name="item-provider-delegate"></a>Representante do provedor de item

Se você não quiser carregar todos os itens na memória, poderá especificar um método delegado do provedor de itens para o parâmetro do componente <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> que recupera de forma assíncrona os itens solicitados sob demanda:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

O provedor de itens recebe um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , que especifica o número necessário de itens começando em um índice de início específico. O provedor de itens recupera os itens solicitados de um banco de dados ou outro serviço e os retorna como um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> juntamente com uma contagem do total de itens. O provedor de itens pode optar por recuperar os itens com cada solicitação ou armazená-los em cache para que estejam prontamente disponíveis.

Um `Virtualize` componente só pode aceitar **uma fonte** de itens de seus parâmetros, portanto, não tente usar um provedor de itens simultaneamente e atribuir uma coleção ao `Items` . Se ambos forem atribuídos, um <xref:System.InvalidOperationException> será gerado quando os parâmetros do componente forem definidos em tempo de execução.

O exemplo a seguir carrega os funcionários de um `EmployeeService` :

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instrui o componente a resolicitar dados do seu <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> . Isso é útil quando dados externos são alterados. Não é necessário chamá-lo ao usar o <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .

## <a name="placeholder"></a>Espaço reservado

Como a solicitação de itens de uma fonte de dados remota pode levar algum tempo, você tem a opção de renderizar um espaço reservado com o conteúdo do item:

* Use um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ) para exibir conteúdo até que os dados do item estejam disponíveis.
* Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> para definir o modelo de item para a lista.

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>Tamanho do item

O tamanho de cada item em pixels pode ser definido com <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (padrão: 50px):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Contagem de sobreverificações

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> Determina quantos itens adicionais são renderizados antes e depois da região visível. Essa configuração ajuda a reduzir a frequência de renderização durante a rolagem. No entanto, valores mais altos resultam em mais elementos renderizados na página (padrão: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Alterações de estado

Ao fazer alterações em itens renderizados pelo `Virtualize` componente, chame <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forçar reavaliação e reprocessamento do componente.
