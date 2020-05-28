---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="file-providers-in-aspnet-core"></a>Provedores de arquivos no ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos. Os provedores de arquivos são usados em todo o ASP.NET Core Framework. Por exemplo:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como `IFileProvider` tipos.
* O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.
* [Razor](xref:mvc/views/razor)usa provedores de arquivos para localizar páginas e exibições.
* As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfaces de provedor de arquivo

A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` expõe métodos para:

* Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)
* Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>

Você pode ler o arquivo usando o <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> método.

O aplicativo de exemplo *FileProviderSample* demonstra como configurar um provedor de arquivos no `Startup.ConfigureServices` para uso em todo o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementações do provedor de arquivos

A tabela a seguir lista as implementações do `IFileProvider` .

| Implementação | Descrição |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | [CompositeFileProvider](#compositefileprovider) | Usado para fornecer acesso combinado a arquivos e diretórios de um ou mais outros provedores. | | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Usado para acessar arquivos inseridos em assemblies. | | [PhysicalFileProvider](#physicalfileprovider) | Usado para acessar os arquivos físicos do sistema. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico. O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos. Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos. O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

Ao instanciar esse provedor diretamente, um caminho de diretório absoluto é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor. Não há suporte para padrões glob no caminho do diretório.

O código a seguir mostra como usar o `PhysicalFileProvider` para obter o conteúdo do diretório e as informações do arquivo:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Tipos no exemplo anterior:

* `provider` é um `IFileProvider`.
* `contents` é um `IDirectoryContents`.
* `fileInfo` é um `IFileInfo`.

O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo. Padrões de glob não podem ser passados para o `GetFileInfo` método. O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.

O aplicativo de exemplo *FileProviderSample* cria o provedor no `Startup.ConfigureServices` método usando <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies. O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.

Para gerar um manifesto dos arquivos inseridos:

1. Adicione o pacote NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) . Embedded ao seu projeto.
1. Defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`. Especifique os arquivos a serem inseridos [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.

O aplicativo de exemplo *FileProviderSample* cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Sobrecargas adicionais permitem:

* Especificar um caminho de arquivo relativo.
* Delimitar os arquivos segundo a data da última modificação.
* Nomear o recurso inserido que contém o manifesto do arquivo inserido.

| Sobrecarga | Descrição |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Aceita um `root` parâmetro de caminho relativo opcional. Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido. | | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Aceita um `root` parâmetro de caminho relativo opcional e `lastModified` um parâmetro date ( <xref:System.DateTimeOffset> ). A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>. | | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Aceita um `root` caminho relativo opcional, uma `lastModified` data e `manifestName` parâmetros. O `manifestName` representa o nome do recurso inserido que contém o manifesto. |

### <a name="compositefileprovider"></a>CompositeFileProvider

O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores. Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.

No aplicativo de exemplo *FileProviderSample* , um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo. O código a seguir é encontrado no método do projeto `Startup.ConfigureServices` :

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Monitorar as alterações

O <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> método fornece um cenário para assistir a um ou mais arquivos ou diretórios para alterações. O método `Watch`:

* Aceita uma cadeia de caracteres de caminho de arquivo, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.
* Retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.

O token de alteração resultante expõe:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Uma propriedade que pode ser inspecionada para determinar se ocorreu uma alteração.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Chamado quando são detectadas alterações na cadeia de caracteres do caminho especificado. Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração. Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.

O aplicativo de exemplo *WatchConsole* grava uma mensagem sempre que um arquivo *. txt* no diretório *textfiles* é modificado:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável. Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.

### <a name="glob-patterns"></a>Padrões glob

Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*. Especifique grupos de arquivos com esses padrões. Os dois caracteres curinga são `*` e `**`:

**`*`**  
Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo. As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.

**`**`**  
Coincide a qualquer coisa em vários níveis de diretório. Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.

A tabela a seguir fornece exemplos comuns de padrões de glob.

|Padrão  |Descrição  |
|---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-----|---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-----|
|`directory/file.txt`| Corresponde a um arquivo específico em um diretório específico. | |`directory/*.txt`| Corresponde a todos os arquivos com a extensão *. txt* em um diretório específico. | |`directory/*/appsettings.json`| Corresponde a todos os arquivos *appSettings. JSON* em diretórios exatamente um nível abaixo da pasta do *diretório* . | |`directory/**/*.txt`| Corresponde a todos os arquivos com uma extensão *. txt* encontrado em qualquer lugar na pasta do *diretório* . |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos. Os provedores de arquivos são usados ​​em toda a estrutura do ASP.NET Core:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como `IFileProvider` tipos.
* O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.
* [Razor](xref:mvc/views/razor)usa provedores de arquivos para localizar páginas e exibições.
* As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfaces de provedor de arquivo

A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` expõe métodos para:

* Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)
* Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>

Você pode ler o arquivo usando o método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).

O aplicativo de amostra demonstra como configurar um provedor de arquivos em `Startup.ConfigureServices` para uso em todo o aplicativo por meio da [injeção de dependência](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementações do provedor de arquivos

Três implementações de `IFileProvider` estão disponíveis.

| Implementação | Descrição |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | [PhysicalFileProvider](#physicalfileprovider) | O provedor físico é usado para acessar os arquivos físicos do sistema. | | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | O provedor inserido do manifesto é usado para acessar arquivos inseridos em assemblies. | | [CompositeFileProvider](#compositefileprovider) | O provedor composto é usado para fornecer acesso combinado a arquivos e diretórios de um ou mais outros provedores. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico. O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos. Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos. O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

Ao criar uma instância para esse provedor, um caminho de diretório é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.

O código a seguir mostra como criar um `PhysicalFileProvider` e usá-lo para obter o conteúdo do diretório e as informações do arquivo:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Tipos no exemplo anterior:

* `provider` é um `IFileProvider`.
* `contents` é um `IDirectoryContents`.
* `fileInfo` é um `IFileInfo`.

O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo. O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.

O aplicativo de amostra cria o provedor na classe `Startup.ConfigureServices` do aplicativo usando [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies. O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.

Para gerar um manifesto dos arquivos inseridos, defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`. Especifique os arquivos a serem inseridos com [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.

O aplicativo de amostra cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Sobrecargas adicionais permitem:

* Especificar um caminho de arquivo relativo.
* Delimitar os arquivos segundo a data da última modificação.
* Nomear o recurso inserido que contém o manifesto do arquivo inserido.

| Sobrecarga | Descrição |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Aceita um `root` parâmetro de caminho relativo opcional. Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido. | | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Aceita um `root` parâmetro de caminho relativo opcional e `lastModified` um parâmetro date ( <xref:System.DateTimeOffset> ). A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>. | | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Aceita um `root` caminho relativo opcional, uma `lastModified` data e `manifestName` parâmetros. O `manifestName` representa o nome do recurso inserido que contém o manifesto. |

### <a name="compositefileprovider"></a>CompositeFileProvider

O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores. Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.

No aplicativo de amostra, um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Monitorar as alterações

O método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona um cenário para monitorar um ou mais arquivos ou diretórios quanto a alterações. `Watch` aceita uma cadeia de caracteres de caminho, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos. `Watch` retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>. O token de alteração expõe:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Uma propriedade que pode ser inspecionada para determinar se ocorreu uma alteração.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Chamado quando são detectadas alterações na cadeia de caracteres do caminho especificado. Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração. Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.

No aplicativo de amostra, o aplicativo de console *WatchConsole* é configurado para exibir uma mensagem sempre que um arquivo de texto é modificado:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável. Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.

## <a name="glob-patterns"></a>Padrões glob

Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*. Especifique grupos de arquivos com esses padrões. Os dois caracteres curinga são `*` e `**`:

**`*`**  
Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo. As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.

**`**`**  
Coincide a qualquer coisa em vários níveis de diretório. Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.

**Exemplos de padrão glob**

**`directory/file.txt`**  
Corresponde a um arquivo específico em um diretório específico.

**`directory/*.txt`**  
Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.

**`directory/*/appsettings.json`**  
Corresponde a todos os arquivos `appsettings.json` em diretórios que estão exatamente um nível abaixo da pasta *diretório*.

**`directory/**/*.txt`**  
Corresponde a todos os arquivos com a extensão *.txt* encontrados em qualquer lugar abaixo da pasta *diretório*.

::: moniker-end
