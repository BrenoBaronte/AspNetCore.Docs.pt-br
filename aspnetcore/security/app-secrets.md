---
title: Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar e recuperar informações confidenciais como segredos do aplicativo durante o desenvolvimento de um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: a12262d182ce84a326086935627b55d2edc4885e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406999"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))

Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo de ASP.NET Core em um computador de desenvolvimento. Nunca armazene senhas ou outros dados confidenciais no código-fonte. Os segredos de produção não devem ser usados para desenvolvimento ou teste. Os segredos não devem ser implantados com o aplicativo. Em vez disso, os segredos devem ser disponibilizados no ambiente de produção por meio de um meio controlado, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger os segredos de produção e de teste do Azure com o [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variáveis de ambiente

As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais. Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.

Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada. Uma cadeia de conexão de banco de dados padrão é incluída naappsettings.jsdo projeto *no* arquivo com a chave `DefaultConnection` . A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha. Durante a implantação do aplicativo, o `DefaultConnection` valor da chave pode ser substituído por um valor de variável de ambiente. A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.

> [!WARNING]
> Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado. Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis. Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gerenciador de segredo

A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core. Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo. Os segredos do aplicativo são armazenados em um local separado da árvore do projeto. Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos. Os segredos do aplicativo não são verificados no controle do código-fonte.

> [!WARNING]
> A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável. É apenas para fins de desenvolvimento. As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.

## <a name="how-the-secret-manager-tool-works"></a>Como funciona a ferramenta Gerenciador de segredo

A ferramenta Gerenciador de segredo abstrai os detalhes da implementação, como onde e como os valores são armazenados. Você pode usar a ferramenta sem conhecer esses detalhes de implementação. Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema no computador local:

# <a name="windows"></a>[Windows](#tab/windows)

Caminho do sistema de arquivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Caminho do sistema de arquivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo `UserSecretsId` valor especificado no arquivo *. csproj* .

Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo. Esses detalhes de implementação podem ser alterados. Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.

## <a name="enable-secret-storage"></a>Habilitar o armazenamento secreto

A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.

A ferramenta Gerenciador de segredo inclui um `init` comando no SDK do .NET Core 3.0.100 ou posterior. Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet user-secrets init
```

O comando anterior adiciona um `UserSecretsId` elemento dentro de um `PropertyGroup` do arquivo *. csproj* . Por padrão, o texto interno de `UserSecretsId` é um GUID. O texto interno é arbitrário, mas é exclusivo para o projeto.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto. Esse gesto adiciona um `UserSecretsId` elemento, populado com um GUID, ao arquivo *. csproj* .

## <a name="set-a-secret"></a>Definir um segredo

Defina um segredo de aplicativo que consiste em uma chave e seu valor. O segredo é associado ao valor do projeto `UserSecretsId` . Por exemplo, execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma `ServiceApiKey` propriedade.

A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios. Use a `--project` opção para fornecer o caminho do sistema de arquivos no qual o arquivo *. csproj* existe. Por exemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Nivelamento de estrutura JSON no Visual Studio

O gesto **gerenciar segredos do usuário** do Visual Studio abre uma *secrets.jsno* arquivo no editor de texto. Substitua o conteúdo de *secrets.js* com os pares chave-valor a serem armazenados. Por exemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set` . Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` a execução recolhe o `Movies` literal do objeto. O arquivo modificado é semelhante ao seguinte:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Definir vários segredos

Um lote de segredos pode ser definido por meio de tubulação JSON para o `set` comando. No exemplo a seguir, o *input.jsno* conteúdo do arquivo é canalizado para o `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acessar um segredo

A [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos do Gerenciador de segredo.

A fonte de configuração segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> para inicializar uma nova instância do host com padrões pré-configurados. `CreateDefaultBuilder`chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> é <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> . Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Os segredos do usuário podem ser recuperados por meio da `Configuration` API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapear segredos para um POCO

O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para mapear os segredos anteriores para um POCO, use o `Configuration` recurso de associação de grafo de [objeto](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API. O código a seguir é associado a um `MovieSettings` poco personalizado e acessa o `ServiceApiKey` valor da propriedade:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e são mapeados para as respectivas propriedades no `MovieSettings` :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Substituição de cadeia de caracteres com segredos

O armazenamento de senhas em texto sem formatação não é seguro. Por exemplo, uma cadeia de conexão de banco de dados armazenada no *appsettings.jsno* pode incluir uma senha para o usuário especificado:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Uma abordagem mais segura é armazenar a senha como um segredo. Por exemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Remova o `Password` par chave-valor da cadeia de conexão no *appsettings.jsem*. Por exemplo:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

O valor do segredo pode ser definido na propriedade de um <xref:System.Data.SqlClient.SqlConnectionStringBuilder> objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para concluir a cadeia de conexão:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Listar os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets list
```

A saída a seguir é exibida:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *secrets.jsem*.

## <a name="remove-a-single-secret"></a>Remover um único segredo

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Osecrets.jsdo aplicativo *no* arquivo foi modificado para remover o par chave-valor associado à `MoviesConnectionString` chave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`exibe a seguinte mensagem:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Remover todos os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets clear
```

Todos os segredos do usuário para o aplicativo foram excluídos do *secrets.jsno* arquivo:

```json
{}
```

`dotnet user-secrets list`A execução exibe a seguinte mensagem:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionais

* Consulte [esse problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerenciador de segredo do IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))

Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo de ASP.NET Core em um computador de desenvolvimento. Nunca armazene senhas ou outros dados confidenciais no código-fonte. Os segredos de produção não devem ser usados para desenvolvimento ou teste. Os segredos não devem ser implantados com o aplicativo. Em vez disso, os segredos devem ser disponibilizados no ambiente de produção por meio de um meio controlado, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger os segredos de produção e de teste do Azure com o [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variáveis de ambiente

As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais. Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.

Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada. Uma cadeia de conexão de banco de dados padrão é incluída naappsettings.jsdo projeto *no* arquivo com a chave `DefaultConnection` . A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha. Durante a implantação do aplicativo, o `DefaultConnection` valor da chave pode ser substituído por um valor de variável de ambiente. A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.

> [!WARNING]
> Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado. Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis. Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gerenciador de segredo

A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core. Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo. Os segredos do aplicativo são armazenados em um local separado da árvore do projeto. Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos. Os segredos do aplicativo não são verificados no controle do código-fonte.

> [!WARNING]
> A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável. É apenas para fins de desenvolvimento. As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.

## <a name="how-the-secret-manager-tool-works"></a>Como funciona a ferramenta Gerenciador de segredo

A ferramenta Gerenciador de segredo abstrai os detalhes da implementação, como onde e como os valores são armazenados. Você pode usar a ferramenta sem conhecer esses detalhes de implementação. Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema no computador local:

# <a name="windows"></a>[Windows](#tab/windows)

Caminho do sistema de arquivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Caminho do sistema de arquivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo `UserSecretsId` valor especificado no arquivo *. csproj* .

Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo. Esses detalhes de implementação podem ser alterados. Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.

## <a name="enable-secret-storage"></a>Habilitar o armazenamento secreto

A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.

Para usar os segredos do usuário, defina um `UserSecretsId` elemento dentro `PropertyGroup` de um do arquivo *. csproj* . O texto interno de `UserSecretsId` é arbitrário, mas é exclusivo para o projeto. Os desenvolvedores normalmente geram um GUID para o `UserSecretsId` .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto. Esse gesto adiciona um `UserSecretsId` elemento, populado com um GUID, ao arquivo *. csproj* .

## <a name="set-a-secret"></a>Definir um segredo

Defina um segredo de aplicativo que consiste em uma chave e seu valor. O segredo é associado ao valor do projeto `UserSecretsId` . Por exemplo, execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma `ServiceApiKey` propriedade.

A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios. Use a `--project` opção para fornecer o caminho do sistema de arquivos no qual o arquivo *. csproj* existe. Por exemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Nivelamento de estrutura JSON no Visual Studio

O gesto **gerenciar segredos do usuário** do Visual Studio abre uma *secrets.jsno* arquivo no editor de texto. Substitua o conteúdo de *secrets.js* com os pares chave-valor a serem armazenados. Por exemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set` . Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` a execução recolhe o `Movies` literal do objeto. O arquivo modificado é semelhante ao seguinte:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Definir vários segredos

Um lote de segredos pode ser definido por meio de tubulação JSON para o `set` comando. No exemplo a seguir, o *input.jsno* conteúdo do arquivo é canalizado para o `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acessar um segredo

A [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos do Gerenciador de segredo.

Se o projeto tiver como alvo .NET Framework, instale o [Microsoft.Extensions.Configuração. Pacote NuGet usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

No ASP.NET Core 2,0 ou posterior, a fonte de configuração de segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> para inicializar uma nova instância do host com padrões pré-configurados. `CreateDefaultBuilder`chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> é <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> no `Startup` Construtor. Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Os segredos do usuário podem ser recuperados por meio da `Configuration` API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapear segredos para um POCO

O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para mapear os segredos anteriores para um POCO, use o `Configuration` recurso de associação de grafo de [objeto](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API. O código a seguir é associado a um `MovieSettings` poco personalizado e acessa o `ServiceApiKey` valor da propriedade:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e são mapeados para as respectivas propriedades no `MovieSettings` :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Substituição de cadeia de caracteres com segredos

O armazenamento de senhas em texto sem formatação não é seguro. Por exemplo, uma cadeia de conexão de banco de dados armazenada no *appsettings.jsno* pode incluir uma senha para o usuário especificado:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Uma abordagem mais segura é armazenar a senha como um segredo. Por exemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Remova o `Password` par chave-valor da cadeia de conexão no *appsettings.jsem*. Por exemplo:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

O valor do segredo pode ser definido na propriedade de um <xref:System.Data.SqlClient.SqlConnectionStringBuilder> objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para concluir a cadeia de conexão:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Listar os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets list
```

A saída a seguir é exibida:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *secrets.jsem*.

## <a name="remove-a-single-secret"></a>Remover um único segredo

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Osecrets.jsdo aplicativo *no* arquivo foi modificado para remover o par chave-valor associado à `MoviesConnectionString` chave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`A execução exibe a seguinte mensagem:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Remover todos os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets clear
```

Todos os segredos do usuário para o aplicativo foram excluídos do *secrets.jsno* arquivo:

```json
{}
```

`dotnet user-secrets list`A execução exibe a seguinte mensagem:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionais

* Consulte [esse problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerenciador de segredo do IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end