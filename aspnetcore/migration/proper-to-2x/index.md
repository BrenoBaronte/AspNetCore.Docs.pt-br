---
title: Migrar do ASP.NET para o ASP.NET Core
author: isaac2004
description: Receba orientações para migrar os aplicativos existentes do ASP.NET MVC ou da API Web para o ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7f5d2835d93631ac73b3da0c3dc26d87ef64c57d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634755"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migrar do ASP.NET para o ASP.NET Core

Por [Isaac Levin](https://isaaclevin.com)

Este artigo serve como um guia de referência para migração de aplicativos ASP.NET para o ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

[SDK do .NET Core 2,2 ou posterior](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Frameworks de destino

Projetos do ASP.NET Core oferecem aos desenvolvedores a flexibilidade de direcionamento para o .NET Core, .NET Framework ou ambos. Consulte [Escolhendo entre o .NET Core e .NET Framework para aplicativos de servidor](/dotnet/standard/choosing-core-framework-server) para determinar qual estrutura de destino é mais apropriada.

Ao usar o .NET Framework como destino, projetos precisam fazer referência a pacotes NuGet individuais.

Usar o .NET Core como destino permite que você elimine várias referências de pacote explícitas, graças ao [metapacote](xref:fundamentals/metapackage-app) do ASP.NET Core. Instale o metapacote `Microsoft.AspNetCore.App` em seu projeto:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Quando o metapacote é usado, nenhum pacote referenciado no metapacote é implantado com o aplicativo. O repositório de runtime do .NET Core inclui esses ativos e eles são pré-compilados para melhorar o desempenho. Para saber mais, confira [Metapacote Microsoft.AspNetCore.App para ASP.NET Core](xref:fundamentals/metapackage-app).

## <a name="project-structure-differences"></a>Diferenças de estrutura do projeto

O formato de arquivo *.csproj* foi simplificado no ASP.NET Core. Algumas alterações importantes incluem:

- A inclusão explícita de arquivos não é necessária para que eles possam ser considerados como parte do projeto. Isso reduz o risco de conflitos de mesclagem XML ao trabalhar em grandes equipes.
- Não há referências baseadas em GUID a outros projetos, o que melhora a legibilidade do arquivo.
- O arquivo pode ser editado sem descarregá-lo no Visual Studio:

    ![Opção de menu de contexto Editar CSPROJ no Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Substituição do arquivo Global.asax

O ASP.NET Core introduziu um novo mecanismo para inicializar um aplicativo. O ponto de entrada para aplicativos ASP.NET é o arquivo *Global.asax*. Tarefas como configuração de roteamento e registros de filtro e de área são tratadas no arquivo *Global.asax*.

[!code-csharp[](samples/globalasax-sample.cs)]

Essa abordagem associa o aplicativo e o servidor no qual ele é implantado de uma forma que interfere na implementação. Em um esforço para desassociar, a [OWIN](https://owin.org/) foi introduzida para fornecer uma maneira mais limpa de usar várias estruturas juntas. A OWIN fornece um pipeline para adicionar somente os módulos necessários. O ambiente de hospedagem leva uma função [Startup](xref:fundamentals/startup) para configurar serviços e pipeline de solicitação do aplicativo. `Startup` registra um conjunto de middleware com o aplicativo. Para cada solicitação, o aplicativo chama cada um dos componentes de middleware com o ponteiro de cabeçalho de uma lista vinculada para um conjunto existente de manipuladores. Cada componente de middleware pode adicionar um ou mais manipuladores para a pipeline de tratamento de solicitação. Isso é feito retornando uma referência para o manipulador que é o novo cabeçalho da lista. Cada manipulador é responsável por se lembrar do próximo manipulador na lista e por invocá-lo. Com o ASP.NET Core, o ponto de entrada para um aplicativo é `Startup` e você não tem mais uma dependência de *Global.asax*. Ao usar a OWIN com o .NET Framework, use algo parecido com o seguinte como um pipeline:

[!code-csharp[](samples/webapi-owin.cs)]

Isso configura as rotas padrão e usa XmlSerialization em Json por padrão. Adicione outro Middleware para este pipeline conforme necessário (carregamento de serviços, definições de configuração, arquivos estáticos, etc.).

O ASP.NET Core usa uma abordagem semelhante, mas não depende de OWIN para manipular a entrada. Em vez disso, isso é feito por meio do método *Program.cs* `Main` (semelhante aos aplicativos de console) e `Startup` é carregado por aí.

[!code-csharp[](samples/program.cs)]

`Startup` deve incluir um método `Configure`. Em `Configure`, adicione o middleware necessário ao pipeline. No exemplo a seguir (com base no modelo de site da Web padrão), os métodos de extensão configuram o pipeline com suporte para:

- Páginas de erro
- Segurança de Transporte Estrita de HTTP
- Redirecionamento de HTTP para HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

O host e o aplicativo foram separados, o que fornece a flexibilidade de mover para uma plataforma diferente no futuro.

> [!NOTE]
> Para obter uma referência mais aprofundada sobre o Startup do ASP.NET Core e middleware, veja [Startup no ASP.NET Core](xref:fundamentals/startup)

## <a name="store-configurations"></a>Armazenar configurações

O ASP.NET dá suporte ao armazenamento de configurações. Essas configurações são usadas, por exemplo, para dar suporte ao ambiente no qual os aplicativos foram implantados. Uma prática comum era armazenar todos os pares chave-valor personalizados na seção `<appSettings>` do arquivo *Web.config*:

[!code-xml[](samples/webconfig-sample.xml)]

Aplicativos leem essas configurações usando a coleção `ConfigurationManager.AppSettings` no namespace `System.Configuration`:

[!code-csharp[](samples/read-webconfig.cs)]

O ASP.NET Core pode armazenar dados de configuração para o aplicativo em qualquer arquivo e carregá-los como parte da inicialização de middleware. O arquivo padrão usado em modelos de projeto é *appsettings.json*:

[!code-json[](samples/appsettings-sample.json)]

Carregar esse arquivo em uma instância de `IConfiguration` dentro de seu aplicativo é feito em *Startup.cs*:

[!code-csharp[](samples/startup-builder.cs)]

O aplicativo lê de `Configuration` para obter as configurações:

[!code-csharp[](samples/read-appsettings.cs)]

Existem extensões para essa abordagem para tornar o processo mais robusto, tais como o uso de DI ([injeção de dependência](xref:fundamentals/dependency-injection)) para carregar um serviço com esses valores. A abordagem de DI fornece um conjunto fortemente tipado de objetos de configuração.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> Para uma referência mais detalhada sobre configuração do ASP.NET Core, veja [Configuração no ASP.NET Core](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Injeção de dependência nativa

Uma meta importante ao criar aplicativos escalonáveis e grandes é o acoplamento flexível de componentes e serviços. A [injeção de dependência](xref:fundamentals/dependency-injection) é uma técnica popular para conseguir isso e é também um componente nativo do ASP.NET Core.

Em aplicativos ASP.NET, os desenvolvedores contam com uma biblioteca de terceiros para implementar injeção de dependência. Um biblioteca desse tipo é a [Unity](https://github.com/unitycontainer/unity), fornecida pelas Diretrizes da Microsoft.

Um exemplo de configuração da injeção de dependência com Unity é a implementação de `IDependencyResolver`, que encapsula uma `UnityContainer`:

[!code-csharp[](samples/sample8.cs)]

Crie uma instância de sua `UnityContainer`, registre seu serviço e defina o resolvedor de dependência de `HttpConfiguration` para a nova instância de `UnityResolver` para o contêiner:

[!code-csharp[](samples/sample9.cs)]

Injete `IProductRepository` quando necessário:

[!code-csharp[](samples/sample5.cs)]

Já que a injeção de dependência é parte do ASP.NET Core, você pode adicionar o serviço no método `ConfigureServices` de *Startup.cs*:

[!code-csharp[](samples/configure-services.cs)]

O repositório pode ser injetado em qualquer lugar, como ocorria com a Unity.

> [!NOTE]
> Para obter mais informações sobre injeção de dependência, confira [Injeção de dependência](xref:fundamentals/dependency-injection).

## <a name="serve-static-files"></a>Fornecer arquivos estáticos

Uma parte importante do desenvolvimento da Web é a capacidade de servir ativos estáticos, do lado do cliente. Os exemplos mais comuns de arquivos estáticos são HTML, CSS, Javascript e imagens. Esses arquivos precisam ser salvos no local de publicação do aplicativo (ou CDN) e referenciados para que eles possam ser carregados por uma solicitação. Esse processo foi alterado no ASP.NET Core.

No ASP.NET, arquivos estáticos são armazenados em vários diretórios e referenciados nas exibições.

Em ASP.NET Core, os arquivos estáticos são armazenados na "raiz da Web" (* &lt; Content root &gt; /wwwroot*), a menos que seja configurado de outra forma. Os arquivos são carregados no pipeline de solicitação invocando o método de extensão `UseStaticFiles` de `Startup.Configure`:

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Se você usar o .NET Framework como destino, instale o pacote NuGet `Microsoft.AspNetCore.StaticFiles`.

Por exemplo, um ativo de imagem na pasta *wwwroot/imagens* está acessível para o navegador em um local como `http://<app>/images/<imageFileName>`.

> [!NOTE]
> Para obter uma referência mais aprofundada sobre como servir arquivos estáticos no ASP.NET Core, veja [Arquivos estáticos](xref:fundamentals/static-files).

## <a name="multi-value-no-loccookies"></a>S de vários valores cookie

Não há suporte para [ cookie s de vários valores](xref:System.Web.HttpCookie.Values) em ASP.NET Core. Crie um cookie por valor.

## <a name="partial-app-migration"></a>Migração de aplicativo parcial

Uma abordagem para a migração de aplicativo parcial é criar um subaplicativo do IIS e mover apenas determinadas rotas de ASP.NET 4. x para ASP.NET Core enquanto preserva a estrutura de URL do aplicativo. Por exemplo, considere a estrutura de URL do aplicativo do arquivo *applicationHost.config* :

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

Estrutura de diretório:

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a>Recursos adicionais

- [Fazendo a portabilidade de Bibliotecas para o .NET Core](/dotnet/core/porting/libraries)
