---
title: Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core
author: scottaddie
description: Saiba mais sobre os benefícios de usar os serviços JavaScript para criar um aplicativo de página única (SPA) apoiado por ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 1b9f5b4b4e066cdd3dd5fbfa666c7a087949979f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054639"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)

Um aplicativo de página única (SPA) é um tipo popular de aplicativo Web devido à sua experiência de usuário sofisticada inerente. A integração de estruturas ou bibliotecas SPA do lado do cliente, como [angular](https://angular.io/) ou [reagir](https://facebook.github.io/react/), com estruturas do lado do servidor, como ASP.NET Core pode ser difícil. Os serviços JavaScript foram desenvolvidos para reduzir o conflito no processo de integração. Ele permite uma operação direta entre as diferentes pilhas de tecnologia de cliente e servidor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Os recursos descritos neste artigo são obsoletos a partir do ASP.NET Core 3,0. Um mecanismo de integração de estruturas SPA mais simples está disponível no pacote NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) . Para obter mais informações, consulte [[comunicado] Obsoleting Microsoft. AspNetCore. SpaServices e Microsoft. AspNetCore. nodeservices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>O que são os serviços JavaScript

Os serviços JavaScript são uma coleção de tecnologias do lado do cliente para ASP.NET Core. Seu objetivo é posicionar ASP.NET Core como a plataforma preferencial do lado do servidor dos desenvolvedores para a criação de SPAs.

Os serviços JavaScript consistem em dois pacotes do NuGet distintos:

* [Microsoft. AspNetCore. nodeservices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (nodeservices)
* [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Esses pacotes são úteis nos seguintes cenários:

* Executar JavaScript no servidor
* Usar uma estrutura ou biblioteca de SPA
* Criar ativos do lado do cliente com o webpack

Grande parte do foco neste artigo é colocado no uso do pacote SpaServices.

## <a name="what-is-spaservices"></a>O que é SpaServices

O SpaServices foi criado para posicionar ASP.NET Core como a plataforma preferencial do lado do servidor dos desenvolvedores para a criação de SPAs. SpaServices não é necessário para desenvolver SPAs com ASP.NET Core e não bloqueia os desenvolvedores em uma estrutura de cliente específica.

O SpaServices fornece uma infraestrutura útil, como:

* [Pré-processamento do lado do servidor](#server-side-prerendering)
* [Middleware de dev do webpack](#webpack-dev-middleware)
* [Substituição de módulo quente](#hot-module-replacement)
* [Auxiliares de roteamento](#routing-helpers)

Coletivamente, esses componentes de infraestrutura aprimoram o fluxo de trabalho de desenvolvimento e a experiência de tempo de execução. Os componentes podem ser adotados individualmente.

## <a name="prerequisites-for-using-spaservices"></a>Pré-requisitos para usar o SpaServices

Para trabalhar com o SpaServices, instale o seguinte:

* [Node.js](https://nodejs.org/) (versão 6 ou posterior) com NPM

  * Para verificar se esses componentes estão instalados e podem ser encontrados, execute o seguinte na linha de comando:

    ```console
    node -v && npm -v
    ```

  * Se estiver implantando em um site do Azure, nenhuma ação será necessária &mdash;Node.js estiver instalada e disponível nos ambientes de servidor.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * No Windows usando o Visual Studio 2017, o SDK é instalado selecionando a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** .

* Pacote NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)

## <a name="server-side-prerendering"></a>Pré-processamento do lado do servidor

Um aplicativo universal (também conhecido como isomórficos) é um aplicativo JavaScript capaz de executar ambos no servidor e no cliente. Angular, reagir e outras estruturas populares fornecem uma plataforma universal para esse estilo de desenvolvimento de aplicativos. A ideia é primeiro renderizar os componentes do Framework no servidor por meio de Node.js e delegar mais execução ao cliente.

ASP.NET Core os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) fornecidos pelo SpaServices simplificam a implementação do pré-processamento do lado do servidor invocando as funções JavaScript no servidor.

### <a name="server-side-prerendering-prerequisites"></a>Pré-requisitos de pré-processamento do lado do servidor

Instale o pacote NPM do [ASPNET-prerenderizando](https://www.npmjs.com/package/aspnet-prerendering) :

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Configuração de pré-processamento do lado do servidor

Os auxiliares de marca são descobertos por meio do registro de namespace no arquivo *_ViewImports. cshtml* do projeto:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Esses auxiliares de marcas abstrairam a complexidade da comunicação direta com APIs de nível baixo, aproveitando uma sintaxe semelhante a HTML dentro da Razor exibição:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>Auxiliar de marca do ASP-PreRender-Module

O `asp-prerender-module` auxiliar de marca, usado no exemplo de código anterior, executa *ClientApp/dist/main-server.js* no servidor via Node.js. Para fins de clareza, *main-server.js* arquivo é um artefato da tarefa Transpilação do TypeScript para JavaScript no processo de Build do [webpack](https://webpack.github.io/) . O webpack define um alias de ponto de entrada de `main-server` ; e a passagem do grafo de dependência para esse alias começa no arquivo *ClientApp/boot-Server. TS* :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

No exemplo angular a seguir, o arquivo *ClientApp/boot-Server. TS* utiliza a `createServerRenderer` função e `RenderResult` o tipo do `aspnet-prerendering` pacote NPM para configurar a renderização do servidor via Node.js. A marcação HTML destinada à renderização do lado do servidor é passada para uma chamada de função de resolução, que é encapsulada em um objeto JavaScript fortemente tipado `Promise` . O `Promise` significado do objeto é que ele fornece de forma assíncrona a marcação HTML para a página para injeção no elemento PlaceHolder do dom.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>ASP-PreRender-auxiliar de marca de dados

Quando combinado com o `asp-prerender-module` auxiliar de marca, o `asp-prerender-data` auxiliar de marca pode ser usado para passar informações contextuais da Razor exibição para o JavaScript do lado do servidor. Por exemplo, a marcação a seguir passa dados do usuário para o `main-server` módulo:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

O `UserName` argumento recebido é serializado usando o serializador JSON interno e é armazenado no `params.data` objeto. No exemplo angular a seguir, os dados são usados para construir uma saudação personalizada dentro de um `h1` elemento:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Os nomes de propriedade passados em auxiliares de marca são representados com notação de **PascalCase** . Compare com JavaScript, em que os mesmos nomes de propriedade são representados com **CamelCase** . A configuração de serialização JSON padrão é responsável por essa diferença.

Para expandir o exemplo de código anterior, os dados podem ser passados do servidor para a exibição por HYDRATING a `globals` propriedade fornecida para a `resolve` função:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

A `postList` matriz definida dentro do `globals` objeto é anexada ao objeto global do navegador `window` . Essa variável que eleva o escopo global elimina a duplicação do esforço, principalmente, pois ela se refere ao carregamento dos mesmos dados uma vez no servidor e novamente no cliente.

![variável de lista de islist global anexada ao objeto Window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Middleware de dev do webpack

O [middleware de dev do webpack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) apresenta um fluxo de trabalho de desenvolvimento simplificado no qual o webpack cria recursos sob demanda. O middleware compila automaticamente e fornece recursos do lado do cliente quando uma página é recarregada no navegador. A abordagem alternativa é invocar manualmente o webpack por meio do script de Build NPM do projeto quando uma dependência de terceiros ou o código personalizado for alterado. Um script de Build NPM na *package.jsno* arquivo é mostrado no exemplo a seguir:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Pré-requisitos de middleware de dev do webpack

Instale o pacote [ASPNET-webpack](https://www.npmjs.com/package/aspnet-webpack) NPM:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Configuração de middleware de dev do webpack

O middleware de dev do webpack é registrado no pipeline de solicitação HTTP por meio do seguinte código no método do arquivo *Startup.cs* `Configure` :

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

O `UseWebpackDevMiddleware` método de extensão deve ser chamado antes de [registrar a hospedagem de arquivo estático](xref:fundamentals/static-files) por meio do `UseStaticFiles` método de extensão. Por motivos de segurança, registre o middleware somente quando o aplicativo for executado no modo de desenvolvimento.

A propriedade do arquivo *webpack.config.js* `output.publicPath` informa ao middleware para ver as `dist` alterações na pasta:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Substituição de módulo quente

Imagine o recurso de [substituição de módulo quente](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) do webpack como uma evolução do [middleware de desenvolvimento do webpack](#webpack-dev-middleware). O HMR apresenta todos os mesmos benefícios, mas simplifica ainda mais o fluxo de trabalho de desenvolvimento atualizando automaticamente o conteúdo da página após a compilação das alterações. Não confunda isso com uma atualização do navegador, que interfere no estado atual da memória e na sessão de depuração do SPA. Há um link ao vivo entre o serviço de middleware de dev do webpack e o navegador, o que significa que as alterações são enviadas por push para o navegador.

### <a name="hot-module-replacement-prerequisites"></a>Pré-requisitos de substituição de módulo quente

Instalar o pacote [webpack – Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) NPM:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Configuração de substituição de módulo quente

O componente HMR deve ser registrado no pipeline de solicitação HTTP do MVC no `Configure` método:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Assim como acontece com o [middleware de dev do webpack](#webpack-dev-middleware), o `UseWebpackDevMiddleware` método de extensão deve ser chamado antes do `UseStaticFiles` método de extensão. Por motivos de segurança, registre o middleware somente quando o aplicativo for executado no modo de desenvolvimento.

O arquivo de *webpack.config.js* deve definir uma `plugins` matriz, mesmo que ela seja deixada vazia:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Depois de carregar o aplicativo no navegador, a guia Console das ferramentas de desenvolvedor fornece a confirmação da ativação do HMR:

![Mensagem conectada de substituição de módulo quente](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Auxiliares de roteamento

Na maioria dos SPAs baseados em ASP.NET Core, o roteamento do lado do cliente geralmente é desejado, além do roteamento do lado do servidor. Os sistemas de roteamento SPA e MVC podem trabalhar independentemente sem interferência. Há, no entanto, um caso de borda apresentando desafios: identificar respostas HTTP 404.

Considere o cenário no qual uma rota de extensão do `/some/page` é usada. Suponha que a solicitação não corresponda por padrão a uma rota do lado do servidor, mas seu padrão corresponde a uma rota do lado do cliente. Agora, considere uma solicitação de entrada para `/images/user-512.png` , que geralmente espera encontrar um arquivo de imagem no servidor. Se esse caminho de recurso solicitado não corresponder a uma rota no lado do servidor ou a um arquivo estático, é improvável que o aplicativo do lado do cliente trate &mdash; normalmente de um código de status HTTP 404.

### <a name="routing-helpers-prerequisites"></a>Pré-requisitos de auxiliares de roteamento

Instale o pacote NPM de roteamento do lado do cliente. Usando angular como um exemplo:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Configuração de auxiliares de roteamento

Um método de extensão chamado `MapSpaFallbackRoute` é usado no `Configure` método:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

As rotas são avaliadas na ordem em que estão configuradas. Consequentemente, a `default` rota no exemplo de código anterior é usada primeiro para correspondência de padrões.

## <a name="create-a-new-project"></a>Criar um novo projeto

Os serviços JavaScript fornecem modelos de aplicativo pré-configurados. O SpaServices é usado nesses modelos em conjunto com estruturas e bibliotecas diferentes, como angular, reagir e Redux.

Esses modelos podem ser instalados por meio do CLI do .NET Core executando o seguinte comando:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Uma lista de modelos SPA disponíveis é exibida:

| Modelos                                 | Nome curto | Idioma | Marcações        |
| ------------------------------------------| :--------: | :------: | :---------: |
| ASP.NET Core MVC com angular             | angular    | [C#]     | Web/MVC/SPA |
| ASP.NET Core MVC com React.js            | react      | [C#]     | Web/MVC/SPA |
| ASP.NET Core MVC com React.js e Redux  | reactredux | [C#]     | Web/MVC/SPA |

Para criar um novo projeto usando um dos modelos SPA, inclua o **nome curto** do modelo no comando [dotnet New](/dotnet/core/tools/dotnet-new) . O comando a seguir cria um aplicativo angular com ASP.NET Core MVC configurado para o lado do servidor:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Definir o modo de configuração de tempo de execução

Existem dois modos de configuração de tempo de execução primários:

* **Desenvolvimento** :
  * Inclui mapas de origem para facilitar a depuração.
  * Não otimiza o código do lado do cliente para desempenho.
* **Produção** :
  * Exclui mapas de origem.
  * Otimiza o código do lado do cliente por meio de agrupamento e minificação.

ASP.NET Core usa uma variável de ambiente chamada `ASPNETCORE_ENVIRONMENT` para armazenar o modo de configuração. Para obter mais informações, consulte [definir o ambiente](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Executar com CLI do .NET Core

Restaure os pacotes NuGet e NPM necessários executando o seguinte comando na raiz do projeto:

```dotnetcli
dotnet restore && npm i
```

Compilar e executar o aplicativo:

```dotnetcli
dotnet run
```

O aplicativo é iniciado no localhost de acordo com o [modo de configuração de tempo de execução](#set-the-runtime-configuration-mode). Navegar até `http://localhost:5000` no navegador exibe a página de aterrissagem.

### <a name="run-with-visual-studio-2017"></a>Executar com o Visual Studio 2017

Abra o arquivo *. csproj* gerado pelo comando [dotnet New](/dotnet/core/tools/dotnet-new) . Os pacotes NuGet e NPM necessários são restaurados automaticamente após a abertura do projeto. Esse processo de restauração pode levar alguns minutos e o aplicativo estará pronto para ser executado quando for concluído. Clique no botão de execução verde ou pressione `Ctrl + F5` e o navegador será aberto na página de aterrissagem do aplicativo. O aplicativo é executado no localhost de acordo com o [modo de configuração de tempo de execução](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Testar o aplicativo

Os modelos SpaServices são pré-configurados para executar testes no lado do cliente usando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/). O Jasmine é uma estrutura de teste de unidade popular para JavaScript, enquanto karma é um executor de teste para esses testes. O karma é configurado para trabalhar com o [middleware de dev do webpack](#webpack-dev-middleware) , de modo que o desenvolvedor não precise parar e executar o teste sempre que forem feitas alterações. Seja o código em execução no caso de teste ou no próprio caso de teste, o teste é executado automaticamente.

Usando o aplicativo angular como um exemplo, dois casos de teste Jasmine já são fornecidos para o `CounterComponent` no arquivo *Counter. Component. spec. TS* :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Abra o prompt de comando no diretório *ClientApp* Execute o seguinte comando:

```console
npm test
```

O script inicia o executor de teste do karma, que lê as configurações definidas no arquivo *karma.conf.js* . Entre outras configurações, o *karma.conf.js* identifica os arquivos de teste a serem executados por meio de sua `files` matriz:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publicar o aplicativo

Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) para obter mais informações sobre como publicar no Azure.

Combinar os ativos gerados do lado do cliente e os artefatos ASP.NET Core publicados em um pacote pronto para implantação pode ser trabalhoso. Felizmente, o SpaServices orquestra todo o processo de publicação com um destino do MSBuild personalizado chamado `RunWebpack` :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

O destino do MSBuild tem as seguintes responsabilidades:

1. Restaure os pacotes NPM.
1. Crie um Build de nível de produção dos ativos de terceiros do lado do cliente.
1. Crie uma compilação de nível de produção dos ativos personalizados do lado do cliente.
1. Copie os ativos gerados pelo webpack para a pasta de publicação.

O destino do MSBuild é invocado ao executar:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Recursos adicionais

* [Documentos angulares](https://angular.io/docs)
