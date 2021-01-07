---
title: Desenvolver ASP.NET Core aplicativos usando o OpenAPI
author: ryanbrandenburg
description: Demonstra como usar a ferramenta ' Microsoft. dotnet-openapi ' para adicionar referências a arquivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 5d9f1684aa333c38c73673138a703b04d318c6df
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972022"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Desenvolver ASP.NET Core aplicativos usando ferramentas OpenAPI

Por Ryan Brandenburg

[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) é uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools) para gerenciar referências do [openapi](https://github.com/OAI/OpenAPI-Specification) em um projeto.

## <a name="installation"></a>Instalação

Para instalar `Microsoft.dotnet-openapi` o, execute o seguinte comando:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Adicionar

Adicionar uma referência de OpenAPI usando qualquer um dos comandos nessa página adiciona um `<OpenApiReference />` elemento semelhante ao seguinte ao arquivo *. csproj* :

```xml
<OpenApiReference Include="openapi.json" />
```

A referência anterior é necessária para que o aplicativo chame o código do cliente gerado.

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Adicionar Arquivo

#### <a name="options"></a>Opções

| Opção curta| Opção Long| Descrição | Exemplo |
|-------|------|-------|---------|
| -p|--updateProject | O projeto no qual operar. |dotnet openapi Adicionar arquivo *--updateProject .\Ref.csproj* .\OpenAPI.js |
| -c|--gerador de código| O gerador de código a ser aplicado à referência. As opções são `NSwagCSharp` e `NSwagTypeScript` . Se `--code-generator` não for especificado, as ferramentas padrão serão `NSwagCSharp` .|dotnet openapi Adicionar arquivo .\OpenApi.jsno gerador-de-código
| -H|--help|Mostra informações da Ajuda|dotnet openapi Adicionar arquivo--ajuda|

#### <a name="arguments"></a>Argumentos

|  Argumento  | Descrição | Exemplo |
|-------------|-------------|---------|
| arquivo de origem | A origem da qual criar uma referência. Deve ser um arquivo OpenAPI. |dotnet openapi Adicionar arquivo *.\OpenAPI.jsem* |

### <a name="add-url"></a>Adicionar URL

#### <a name="options"></a>Opções

| Opção curta| Opção Long| Descrição | Exemplo |
|-------|------|-------------|---------|
| -p|--updateProject | O projeto no qual operar. |dotnet openapi Adicionar URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -o|--arquivo de saída | Onde posicionar a cópia local do arquivo OpenAPI. |dotnet openapi Adicionar URL `https://contoso.com/openapi.json` *--arquivo de saída myclient.jsem* |
| -c|--gerador de código| O gerador de código a ser aplicado à referência. As opções são `NSwagCSharp` e `NSwagTypeScript` . |dotnet openapi Adicionar URL `https://contoso.com/openapi.json` --gerador de código
| -H|--help|Mostra informações da Ajuda|dotnet openapi Adicionar URL--ajuda|

#### <a name="arguments"></a>Argumentos

|  Argumento  | Descrição | Exemplo |
|-------------|-------------|---------|
| origem-URL | A origem da qual criar uma referência. Deve ser uma URL. |dotnet openapi Adicionar URL `https://contoso.com/openapi.json` |

## <a name="remove"></a>Remover

Remove a referência OpenAPI correspondente ao nome do arquivo *. csproj* fornecido. Quando a referência de OpenAPI for removida, os clientes não serão gerados. Os arquivos local *. JSON* e *. YAML* são excluídos.

### <a name="options"></a>Opções

| Opção curta| Opção Long| Descrição| Exemplo |
|-------|------|------------|---------|
| -p|--updateProject | O projeto no qual operar. |dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.js |
| -H|--help|Mostra informações da Ajuda|dotnet openapi remover--ajuda|

### <a name="arguments"></a>Argumentos

|  Argumento  | Descrição| Exemplo |
| ------------|------------|---------|
| arquivo de origem | A origem para a qual remover a referência. |dotnet openapi remover *.\OpenAPI.js* |

## <a name="refresh"></a>Atualizar

Atualiza a versão local de um arquivo que foi baixado usando o conteúdo mais recente da URL de download.

### <a name="options"></a>Opções

| Opção curta| Opção Long| Descrição | Exemplo |
|-------|------|-------------|---------|
| -p|--updateProject | O projeto no qual operar. | dotnet openapi atualização *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -H|--help|Mostra informações da Ajuda|dotnet openapi atualização--ajuda|

### <a name="arguments"></a>Argumentos

|  Argumento  | Descrição | Exemplo |
| ------------|-------------|---------|
| origem-URL | A URL da qual atualizar a referência. | atualização do dotnet openapi `https://contoso.com/openapi.json` |
