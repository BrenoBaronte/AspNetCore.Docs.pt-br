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
ms.openlocfilehash: 30baab0649268f4abf0dbd6c99dfeef3f43d0054
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536268"
---
A maneira preferida de ler valores de configuração relacionados é usar o [padrão de opções](xref:fundamentals/configuration/options). Por exemplo, para ler os seguintes valores de configuração:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Crie a seguinte `PositionOptions` classe:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Uma classe de opções:

* Deve ser não-abstrato com um construtor público sem parâmetros.
* Todas as propriedades de leitura/gravação públicas do tipo estão associadas.
* Os campos ***não*** estão associados. No código anterior, `Position` não está associado. A `Position` propriedade é usada para que a cadeia de caracteres `"Position"` não precise ser embutida em código no aplicativo ao associar a classe a um provedor de configuração.

O seguinte código:

* Chama [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) para associar a `PositionOptions` classe à `Position` seção.
* Exibe os `Position` dados de configuração.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

No código anterior, por padrão, as alterações no arquivo de configuração JSON depois que o aplicativo é iniciado são lidas.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado. `ConfigurationBinder.Get<T>` pode ser mais conveniente do que usar o `ConfigurationBinder.Bind` . O código a seguir mostra como usar `ConfigurationBinder.Get<T>` com a `PositionOptions` classe:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

No código anterior, por padrão, as alterações no arquivo de configuração JSON depois que o aplicativo é iniciado são lidas.

Uma abordagem alternativa ao usar o **padrão de opções*** é associar a `Position` seção e adicioná-la ao contêiner de serviço de injeção de [dependência](xref:fundamentals/dependency-injection). No código a seguir, `PositionOptions` é adicionado ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> e associado à configuração:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Usando o código anterior, o código a seguir lê as opções de posição:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

No código anterior, as alterações no arquivo de configuração JSON depois que o aplicativo foi iniciado ***não*** são lidas. Para ler as alterações depois que o aplicativo for iniciado, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).
