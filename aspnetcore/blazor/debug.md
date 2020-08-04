---
title: ASP.NET Core de depuraçãoBlazor WebAssembly
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: cb0a8737fb975db285986d18b995e488f09580e8
ms.sourcegitcommit: 37f6f2e13ceb4eae268d20973d76e4b83acf6a24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526284"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core de depuraçãoBlazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssemblyos aplicativos podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome). Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.

Os cenários disponíveis incluem:

* Definir e remover pontos de interrupção.
* Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).
* Etapa única (<kbd>F10</kbd>) por meio do código.
* Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.
* Na exibição *locais* , observe os valores de variáveis locais.
* Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.

Por enquanto, você *não pode*:

* Interromper em exceções sem tratamento.
* Pontos de interrupção de acesso durante a inicialização do aplicativo.

Continuaremos a melhorar a experiência de depuração em versões futuras.

## <a name="prerequisites"></a>Pré-requisitos

A depuração requer um dos seguintes navegadores:

* Google Chrome (versão 70 ou posterior) (padrão)
* Microsoft Edge (versão 80 ou posterior)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Habilitar depuração para Visual Studio e Visual Studio Code

Para habilitar a depuração para um Blazor WebAssembly aplicativo existente, atualize o `launchSettings.json` arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Uma vez atualizado, o `launchSettings.json` arquivo deve ser semelhante ao exemplo a seguir:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

A `inspectUri` Propriedade:

* Permite que o IDE detecte que o aplicativo é um Blazor WebAssembly aplicativo.
* Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.

Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.

## <a name="visual-studio"></a>Visual Studio

Para depurar um Blazor WebAssembly aplicativo no Visual Studio:

1. Crie um novo aplicativo hospedado ASP.NET Core Blazor WebAssembly .
1. Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.
1. Defina um ponto de interrupção no `Pages/Counter.razor` no `IncrementCount` método.
1. Navegue até a **`Counter`** guia e selecione o botão para atingir o ponto de interrupção:

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Confira o valor do `currentCount` campo na janela locais:

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Pressione <kbd>F5</kbd> para continuar a execução.

Ao depurar seu Blazor WebAssembly aplicativo, você também pode depurar o código do servidor:

1. Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.
1. Navegue até a **`Fetch Data`** guia para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor:

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` :

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

### <a name="debug-standalone-no-locblazor-webassembly"></a>Depurar autônomoBlazor WebAssembly

1. Abra o Blazor WebAssembly aplicativo autônomo no vs Code.

   Você pode receber a seguinte notificação de que a configuração adicional é necessária para habilitar a depuração:
   
   ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   Se você receber a notificação:

   * Confirme se a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) está instalada. Para inspecionar as extensões instaladas, abra **Exibir**  >  **extensões** na barra de menus ou selecione o ícone **extensões** na barra lateral **atividade** .
   * Confirme se a depuração de visualização do JavaScript está habilitada. Abra as configurações na barra de menus (configurações de preferências de**arquivo**  >  **Preferences**  >  **Settings**). Pesquise usando as palavras-chave `debug preview` . Nos resultados da pesquisa, confirme se a caixa de seleção para **depurar > JavaScript: usar visualização** está marcada.
   * Recarregue a janela.

1. Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.

1. Quando solicitado, selecione a opção de ** Blazor WebAssembly depuração** para iniciar a depuração.

   ![Lista de opções de depuração disponíveis](index/_static/blazor-vscode-debugtypes.png)

1. O aplicativo autônomo é iniciado e um navegador de depuração é aberto.

1. Defina um ponto de interrupção no `IncrementCount` método no `Counter` componente e, em seguida, selecione o botão para atingir o ponto de interrupção:

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a>Depuração hospedadaBlazor WebAssembly

1. Abra a Blazor WebAssembly pasta de solução do aplicativo hospedado no vs Code.

1. Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida. Selecione **Sim** na barra superior.

   ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Na paleta de comandos na parte superior da janela, selecione o projeto de *servidor* dentro da solução hospedada.

Um `launch.json` arquivo é gerado com a configuração de inicialização para iniciar o depurador.

### <a name="attach-to-an-existing-debugging-session"></a>Anexar a uma sessão de depuração existente

Para anexar a um aplicativo em execução Blazor , crie um `launch.json` arquivo com a seguinte configuração:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos. Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.

### <a name="launch-configuration-options"></a>Opções de configuração de inicialização

As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração ( `.vscode/launch.json` ).

| Opção    | Descrição |
| --------- | ----------- |
| `request` | Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor WebAssembly aplicativo ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução. |
| `url`     | A URL a ser aberta no navegador durante a depuração. O padrão é `https://localhost:5001`. |
| `browser` | O navegador a ser iniciado para a sessão de depuração. Definir como `edge` ou `chrome`. O padrão é `chrome`. |
| `trace`   | Usado para gerar logs do depurador JS. Defina como `true` para gerar logs. |
| `hosted`  | Deve ser definido como `true` se estiver iniciando e depurando um Blazor WebAssembly aplicativo hospedado. |
| `webRoot` | Especifica o caminho absoluto do servidor Web. Deve ser definido se um aplicativo for servido de uma sub-roteiro. |
| `timeout` | O número de milissegundos a aguardar a sessão de depuração ser anexada. O padrão é 30.000 milissegundos (30 segundos). |
| `program` | Uma referência ao executável para executar o servidor do aplicativo hospedado. Deve ser definido se `hosted` for `true` . |
| `cwd`     | O diretório de trabalho para o qual iniciar o aplicativo. Deve ser definido se `hosted` for `true` . |
| `env`     | As variáveis de ambiente a serem fornecidas ao processo iniciado. Aplicável somente se `hosted` for definido como `true` . |

### <a name="example-launch-configurations"></a>Exemplo de configurações de inicialização

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Iniciar e depurar um Blazor WebAssembly aplicativo autônomo

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Anexar a um aplicativo em execução em uma URL especificada

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Iniciar e depurar um Blazor WebAssembly aplicativo hospedado com o Microsoft Edge

A configuração do navegador usa como padrão o Google Chrome. Ao usar o Microsoft Edge para depuração, defina `browser` como `edge` . Para usar o Google Chrome, não defina a `browser` opção ou defina o valor da opção como `chrome` .

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

No exemplo anterior, `MyHostedApp.Server.dll` é o assembly do aplicativo do *servidor* . A `.vscode` pasta está localizada na pasta da solução ao lado das `Client` pastas, `Server` e `Shared` .

## <a name="debug-in-the-browser"></a>Depurar no navegador

1. Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.

1. Inicie um navegador e navegue até a URL do aplicativo (por exemplo, `https://localhost:5001` ).

1. No navegador, tente iniciar a depuração remota pressionando <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

   O navegador deve estar em execução com a depuração remota habilitada, que não é o padrão. Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será renderizada com instruções para iniciar o navegador com a porta de depuração aberta. Siga as instruções para seu navegador, que abre uma nova janela do navegador. Feche a janela anterior do navegador.

1. Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração (<kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) abrirá uma nova guia do depurador.

1. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net do aplicativo dentro do `file://` nó.

1. No código do componente ( `.razor` arquivos) e em arquivos de código C# ( `.cs` ), os pontos de interrupção definidos por você são atingidos quando o código é executado. Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.

Blazorfornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede. Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy. O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).

## <a name="browser-source-maps"></a>Mapas de origem do navegador

Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente. No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM. Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.

## <a name="troubleshoot"></a>Solucionar problemas

Se você estiver executando erros, as dicas a seguir podem ajudar:

* Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador. No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.
* Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS. Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* O Visual Studio requer a opção **Habilitar depuração JavaScript para ASP.net (Chrome, Edge e IE)** em **ferramentas**  >  **Opções**  >  **depuração**  >  **geral**. Essa é a configuração padrão para o Visual Studio. Se a depuração não estiver funcionando, confirme se a opção está selecionada.

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>Pontos de interrupção em `OnInitialized{Async}` não atingido

O Blazor proxy de depuração da estrutura leva pouco tempo para ser iniciado, portanto, os pontos de interrupção no [ `OnInitialized{Async}` método de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) podem não ser atingidos. É recomendável adicionar um atraso no início do corpo do método para dar ao proxy de depuração algum tempo para iniciar antes de o ponto de interrupção ser atingido. Você pode incluir o atraso com base em uma [ `if` diretiva de compilador](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para garantir que o atraso não esteja presente para uma compilação de versão do aplicativo.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```
