---
title: Testar APIs da Web com o HttpRepl
author: scottaddie
description: Saiba como usar a ferramenta global do HttpRepl .NET Core para navegar e testar uma API da Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2020
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
uid: web-api/http-repl
ms.openlocfilehash: 1027887738740d50c30e24e800c0402b1ce4ad02
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854659"
---
# <a name="test-web-apis-with-the-httprepl"></a>Testar APIs da Web com o HttpRepl

Por [Scott Addie](https://twitter.com/Scott_Addie)

O HTTP REPL (Read-Eval-Print Loop) é:

* Uma ferramenta de linha de comando leve e multiplataforma compatível com todos os recursos compatíveis com o .NET Core.
* Usado para fazer solicitações HTTP a fim de testar as APIs Web do ASP.NET Core (e as APIs Web não pertencentes ao ASP.NET Core) e exibir os resultados.
* Capaz de testar APIs Web hospedadas em qualquer ambiente, inclusive no localhost e no Serviço de Aplicativo do Azure.

Os [verbos HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) a seguir são compatíveis:

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [PRINCIPAL](#test-http-head-requests)
* [Opções](#test-http-options-requests)
* [DISTRIBUÍDO](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

Para acompanhar, [exiba ou baixe a API Web de exemplo do ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Instalação

Para instalar o HttpRepl, execute o seguinte comando:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

Uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) é instalada pelo pacote do NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).

## <a name="usage"></a>Uso

Após a instalação bem-sucedida da ferramenta, execute o seguinte comando para iniciar o HttpRepl:

```console
httprepl
```

Para exibir os comandos HttpRepl disponíveis, execute um dos seguintes comandos:

```console
httprepl -h
```

```console
httprepl --help
```

É exibida a saída a seguir:

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

O HttpRepl oferece a conclusão do comando. Pressionar a tecla <kbd>Tab</kbd> itera na lista de comandos que completam os caracteres ou o ponto de extremidade da API que você digitou. As seções a seguir descrevem os comandos da CLI disponíveis.

## <a name="connect-to-the-web-api"></a>Conectar-se à API Web

Conecte-se à uma API Web executando o seguinte comando:

```console
httprepl <ROOT URI>
```

`<ROOT URI>` é o URI de base para a API Web. Por exemplo:

```console
httprepl https://localhost:5001
```

Como alternativa, execute o seguinte comando a qualquer momento enquanto o HttpRepl estiver em execução:

```console
connect <ROOT URI>
```

Por exemplo:

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a>Apontar manualmente para a descrição do OpenAPI para a API Web

O comando Connect acima tentará localizar a descrição de OpenAPI automaticamente. Se, por algum motivo, não for possível fazer isso, você poderá especificar o URI da descrição de OpenAPI para a API Web usando a `--openapi` opção:

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

Por exemplo:

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a>Habilitar saída detalhada para obter detalhes sobre a pesquisa, análise e validação de descrição de OpenAPI

A especificação da `--verbose` opção com o `connect` comando produzirá mais detalhes quando a ferramenta procurar a descrição de openapi, analisá-la e validá-la.

```console
connect <ROOT URI> --verbose
```

Por exemplo:

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a>Navegar na API Web

### <a name="view-available-endpoints"></a>Exibir os pontos de extremidade disponíveis

Para listar os diferentes pontos de extremidade (controladores) no caminho atual do endereço da API Web, execute os comandos `ls` ou `dir`:

```console
https://localhost:5001/> ls
```

O seguinte formato de saída será exibido:

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

A saída anterior indica que há dois controladores disponíveis: `Fruits` e `People`. Ambos os controladores são compatíveis com operações HTTP GET e POST sem parâmetro.

Navegar em um controlador específico revela mais detalhes. Por exemplo, a saída do seguinte comando mostra que o controlador `Fruits` também é compatível com as operações HTTP GET, PUT e DELETE. Cada uma dessas operações espera um parâmetro `id` na rota:

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

Outra opção é executar o comando `ui` para abrir a página da interface do usuário do Swagger da API Web em um navegador. Por exemplo:

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a>Navegar até um ponto de extremidade

Para navegar até um ponto de extremidade diferente na API Web, execute o comando `cd`:

```console
https://localhost:5001/> cd people
```

O caminho após o comando `cd` não diferencia maiúsculas de minúsculas. O seguinte formato de saída será exibido:

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a>Personalizar o HttpRepl

As [cores](#set-color-preferences) padrão do HttpRepl podem ser personalizadas. Além disso, um [editor de texto padrão](#set-the-default-text-editor) pode ser definido. As preferências de HttpRepl são mantidas na sessão atual e são respeitadas em sessões futuras. Depois de modificadas, as preferências são armazenadas no seguinte arquivo:

# <a name="linux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

O arquivo *.httpreplprefs* é carregado na inicialização e suas alterações não são monitoradas no runtime. As modificações manuais no arquivo entram em vigor somente após a reinicialização da ferramenta.

### <a name="view-the-settings"></a>Exibir as configurações

Para exibir as configurações disponíveis, execute o comando `pref get`. Por exemplo:

```console
https://localhost:5001/> pref get
```

O comando anterior exibe os pares chave-valor disponíveis:

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a>Definir preferências de cores

No momento, as cores das respostas só são compatíveis com JSON. Para personalizar a coloração padrão da ferramenta HttpRepl, localize a chave correspondente à cor a ser alterada. Para ver instruções sobre como localizar as chaves, confira a seção [Exibir as configurações](#view-the-settings). Por exemplo, altere o valor da chave `colors.json` de `Green` para `White`, desta forma:

```console
https://localhost:5001/people> pref set colors.json White
```

Somente as [cores permitidas](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) podem ser usadas. As solicitações HTTP subsequentes exibem a saída com a nova cor.

Quando chaves de cor específicas não estão definidas, mais chaves genéricas são consideradas. Para demonstrar esse comportamento de fallback, considere o seguinte exemplo:

* Se `colors.json.name` não tiver um valor, `colors.json.string` será usado.
* Se `colors.json.string` não tiver um valor, `colors.json.literal` será usado.
* Se `colors.json.literal` não tiver um valor, `colors.json` será usado. 
* Se `colors.json` não tiver um valor, a cor de texto padrão do shell de comando (`AllowedColors.None`) será usada.

### <a name="set-indentation-size"></a>Definir o tamanho do recuo

A personalização do tamanho do recuo da resposta só é compatível com JSON. O tamanho padrão é dois espaços. Por exemplo:

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

Para alterar o tamanho padrão, defina a chave `formatting.json.indentSize`. Por exemplo, para sempre usar quatro espaços:

```console
pref set formatting.json.indentSize 4
```

As respostas subsequentes respeitarão a configuração de quatro espaços:

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a>Definir o editor de texto padrão

Por padrão, o HttpRepl não tem nenhum editor de texto configurado para uso. Para testar os métodos de API Web que exigem o corpo da solicitação HTTP, é preciso definir um editor de texto padrão. A ferramenta HttpRepl inicia o editor de texto configurado para o único propósito de compor o corpo da solicitação. Execute o seguinte comando para definir o editor de texto preferido como padrão:

```console
pref set editor.command.default "<EXECUTABLE>"
```

No comando anterior, `<EXECUTABLE>` é o caminho completo para o arquivo executável do editor de texto. Por exemplo, execute o seguinte comando para definir o Visual Studio Code como o editor de texto padrão:

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Para iniciar o editor de texto padrão com argumentos específicos da CLI, defina a chave `editor.command.default.arguments`. Por exemplo, suponha que Visual Studio Code seja o editor de texto padrão e que você sempre queira que o HttpRepl abra Visual Studio Code em uma nova sessão com as extensões desabilitadas. Execute o comando a seguir:

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> Se o editor padrão for Visual Studio Code, você geralmente desejará passar o `-w` `--wait` argumento ou para forçar Visual Studio Code aguardar para fechar o arquivo antes de retornar.

### <a name="set-the-openapi-description-search-paths"></a>Definir os caminhos de pesquisa de descrição do OpenAPI

Por padrão, o HttpRepl tem um conjunto de caminhos relativos que ele usa para localizar a descrição de OpenAPI ao executar o `connect` comando sem a `--openapi` opção. Esses caminhos relativos são combinados com os caminhos raiz e base especificados no comando `connect`. Os caminhos relativos padrão são:

- *swagger.jsem*
- *Swagger/v1/swagger.jsem*
- */swagger.jsem*
- */swagger/v1/swagger.json*
- *openapi.jsem*
- */openapi.jsem*

Para usar um conjunto diferente de caminhos de pesquisa em seu ambiente, defina a preferência `swagger.searchPaths`. O valor precisa ser uma lista delimitada por pipes de caminhos relativos. Por exemplo:

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

Em vez de substituir a lista padrão totalmente, a lista também pode ser modificada adicionando ou removendo caminhos.

Para adicionar um ou mais caminhos de pesquisa à lista padrão, defina a `swagger.addToSearchPaths` preferência. O valor precisa ser uma lista delimitada por pipes de caminhos relativos. Por exemplo:

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

Para remover um ou mais caminhos de pesquisa da lista padrão, defina a `swagger.addToSearchPaths` preferência. O valor precisa ser uma lista delimitada por pipes de caminhos relativos. Por exemplo:

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a>Testar solicitações HTTP GET

### <a name="synopsis"></a>Sinopse

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumentos

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `get`:

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Exemplo

Para emitir uma solicitação HTTP GET:

1. Execute o comando `get` em um ponto de extremidade compatível:

    ```console
    https://localhost:5001/people> get
    ```

    O comando anterior exibirá o seguinte formato de saída:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people>
    ```

1. Recupere um registro específico passando um parâmetro para o comando `get`:

    ```console
    https://localhost:5001/people> get 2
    ```

    O comando anterior exibirá o seguinte formato de saída:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a>Testar solicitações HTTP POST

### <a name="synopsis"></a>Sinopse

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumentos

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Exemplo

Para emitir uma solicitação HTTP POST:

1. Execute o comando `post` em um ponto de extremidade compatível:

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON. O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP. Por exemplo:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).

1. Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. Salve o arquivo *.tmp* e feche o editor de texto. A seguinte saída será exibida no shell de comando:

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a>Testar solicitações HTTP PUT

### <a name="synopsis"></a>Sinopse

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumentos

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Exemplo

Para emitir uma solicitação HTTP PUT:

1. *Opcional*: execute o `get` comando para exibir os dados antes de modificá-los:

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Execute o comando `put` em um ponto de extremidade compatível:

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON. O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP. Por exemplo:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).

1. Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Salve o arquivo *.tmp* e feche o editor de texto. A seguinte saída será exibida no shell de comando:

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Opcional*: emita um `get` comando para ver as modificações. Por exemplo, se você digitar "Cherry" no editor de texto, um `get` retornará a seguinte saída:

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a>Testar solicitações HTTP DELETE

### <a name="synopsis"></a>Sinopse

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumentos

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Exemplo

Para emitir uma solicitação HTTP DELETE:

1. *Opcional*: execute o `get` comando para exibir os dados antes de modificá-los:

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Execute o comando `delete` em um ponto de extremidade compatível:

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    O comando anterior exibirá o seguinte formato de saída:

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Opcional*: emita um `get` comando para ver as modificações. Neste exemplo, um `get` retorna a seguinte saída:

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a>Testar solicitações HTTP PATCH

### <a name="synopsis"></a>Sinopse

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumentos

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Testar solicitações HTTP HEAD

### <a name="synopsis"></a>Sinopse

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumentos

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Testar solicitações HTTP OPTIONS

### <a name="synopsis"></a>Sinopse

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumentos

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Configurar cabeçalhos de solicitações HTTP

Para configurar um cabeçalho de solicitação HTTP, use uma das seguintes abordagens:

* Configure embutido com a solicitação HTTP. Por exemplo:

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    Com a abordagem anterior, cada cabeçalho de solicitação HTTP diferente exige sua própria opção `-h`.

* Configure antes de enviar a solicitação HTTP. Por exemplo:

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    Ao configurar o cabeçalho antes de enviar a solicitação, ele permanecerá configurado durante toda a sessão do shell de comando. Para limpar o cabeçalho, forneça um valor vazio. Por exemplo:
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>Pontos de extremidade protegidos de teste

O HttpRepl dá suporte ao teste de pontos de extremidade protegidos das seguintes maneiras:

* Por meio das credenciais padrão do usuário conectado.
* Por meio do uso de cabeçalhos de solicitação HTTP.

### <a name="default-credentials"></a>Credenciais padrão

Considere uma API Web que você está testando no IIS e protegida com a autenticação do Windows. Você deseja que as credenciais do usuário que executa a ferramenta fluam para os pontos de extremidade HTTP que estão sendo testados. Para passar as credenciais padrão do usuário conectado:

1. Defina a `httpClient.useDefaultCredentials` preferência como `true` :

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. Saia e reinicie a ferramenta antes de enviar outra solicitação para a API da Web.
 
### <a name="default-proxy-credentials"></a>Credenciais de proxy padrão

Considere um cenário no qual a API Web que você está testando está atrás de um proxy protegido com a autenticação do Windows. Você deseja que as credenciais do usuário que executa a ferramenta fluam para o proxy. Para passar as credenciais padrão do usuário conectado:

1. Defina a `httpClient.proxy.useDefaultCredentials` preferência como `true` :

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. Saia e reinicie a ferramenta antes de enviar outra solicitação para a API da Web.

### <a name="http-request-headers"></a>Cabeçalhos de solicitação HTTP

Entre os exemplos de esquemas de autenticação e autorização com suporte estão:

* basic authentication
* Tokens de portador JWT
* autenticação resumida

Por exemplo, você pode enviar um token de portador para um ponto de extremidade com o seguinte comando:

```console
set header Authorization "bearer <TOKEN VALUE>"
```

Para acessar um ponto de extremidade hospedado no Azure ou usar a [API REST do Azure](/rest/api/azure/), você precisa de um token de portador. Use as etapas a seguir para obter um token de portador para sua assinatura do Azure por meio do [CLI do Azure](/cli/azure/). O HttpRepl define o token de portador em um cabeçalho de solicitação HTTP. Uma lista de aplicativos Web do serviço de Azure App é recuperada.

1. Entrar no Azure:

    ```azurecli
    az login
    ```

1. Obtenha sua ID de assinatura com o seguinte comando:

    ```azurecli
    az account show --query id
    ```

1. Copie sua ID de assinatura e execute o seguinte comando:

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. Obtenha seu token de portador com o seguinte comando:

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. Conecte-se à API REST do Azure por meio do HttpRepl:

    ```console
    httprepl https://management.azure.com
    ```

1. Defina o `Authorization` cabeçalho da solicitação http:

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. Navegue até a assinatura:

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. Obtenha uma lista dos aplicativos Web do serviço de Azure App da sua assinatura:

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    A seguinte resposta é exibida:

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a>Alternar exibição da solicitação HTTP

Por padrão, a exibição da solicitação HTTP que está sendo enviada é suprimida. É possível alterar a configuração correspondente durante a sessão do shell de comando.

### <a name="enable-request-display"></a>Habilitar a exibição da solicitação

Exiba a solicitação HTTP que está sendo enviada executando o comando `echo on`. Por exemplo:

```console
https://localhost:5001/people> echo on
Request echoing is on
```

As solicitações HTTP subsequentes na sessão atual exibem os cabeçalhos de solicitação. Por exemplo:

```console
https://localhost:5001/people> post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people>
```

### <a name="disable-request-display"></a>Desabilitar a exibição da solicitação

Suprima a exibição da solicitação HTTP que está sendo enviada executando o comando `echo off`. Por exemplo:

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a>Executar um script

Se você executar com frequência o mesmo conjunto de comandos HttpRepl, considere armazená-los em um arquivo de texto. Os comandos no arquivo assumem a mesma forma como comandos executados manualmente na linha de comando. Os comandos podem ser executados em um modo em lote usando o comando `run`. Por exemplo:

1. Crie um arquivo de texto com um conjunto de comandos delimitados por nova linha. Para ilustrar, considere um arquivo *people-script.txt* com os seguintes comandos:

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Execute o comando `run`, passando o caminho do arquivo de texto. Por exemplo:

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    O seguinte resultado é exibido:

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a>Limpar a saída

Para remover toda a saída gravada no Shell de comando pela ferramenta HttpRepl, execute `clear` o `cls` comando ou. Para ilustrar, imagine que o shell de comando contém a seguinte saída:

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

Execute o comando a seguir para limpar a saída:

```console
https://localhost:5001/> clear
```

Após a execução o comando anterior, o shell de comando conterá somente a seguinte saída:

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a>Recursos adicionais

* [Solicitações da API REST](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [Repositório GitHub do HttpRepl](https://github.com/dotnet/HttpRepl)
* [Configurar o Visual Studio para iniciar o HttpRepl](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-windows-to-launch-httprepl-on-f5)
* [Configurar Visual Studio Code para iniciar o HttpRepl](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-code-to-launch-httprepl-on-debug)
* [Configurar Visual Studio para Mac para iniciar o HttpRepl](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-mac-to-launch-httprepl-as-a-custom-tool)
