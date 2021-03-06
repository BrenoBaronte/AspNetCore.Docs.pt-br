---
title: Carregar arquivos no ASP.NET Core
author: rick-anderson
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: c32d20d4616650db004c78fb4d8ea9a4d5a3beab
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252793"
---
# <a name="upload-files-in-aspnet-core"></a>Carregar arquivos no ASP.NET Core

Por [Steve Smith](https://ardalis.com/) e [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-5.0"

O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considerações de segurança

Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor. Os invasores podem tentar:

* Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .
* Carregar vírus ou malware.
* Comprometa redes e servidores de outras maneiras.

As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:

* Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema. Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados. Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;
* **Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;
* Use um nome de arquivo seguro determinado pelo aplicativo. Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; O HTML codifica o nome de arquivo não confiável ao exibi-lo. Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (a Razor saída de código HTML automaticamente).
* Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Verifique se as verificações do lado do cliente são executadas no servidor. &dagger; As verificações no lado do cliente são fáceis de evitar.
* Verifique o tamanho de um arquivo carregado. Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;
* Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.
* **Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**

&dagger;O aplicativo de exemplo demonstra uma abordagem que atende aos critérios.

> [!WARNING]
> Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:
>
> * Assuma completamente o controle de um sistema.
> * Sobrecarregar um sistema com o resultado que o sistema falha.
> * Comprometer dados do sistema ou de usuários.
> * Aplique graffiti a uma interface do usuário pública.
>
> Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
> * [Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .

## <a name="storage-scenarios"></a>Cenários de armazenamento

As opções de armazenamento comuns para arquivos incluem:

* Banco de dados

  * Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).
  * Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).
  * Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.

* Armazenamento físico (sistema de arquivos ou compartilhamento de rede)

  * Para carregamentos de arquivos grandes:
    * Os limites de banco de dados podem restringir o tamanho do carregamento.
    * O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.
  * O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.
  * O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento. **Nunca conceda a permissão EXECUTE.**

* Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))

  * Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.
  * Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.

  Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Cenários de upload de arquivo

Duas abordagens gerais para carregar arquivos são buffer e streaming.

**de resposta**

O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile> , que é uma representação em C# do arquivo usado para processar ou salvar o arquivo.

Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos. Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco. Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.

> [!NOTE]
> Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.

O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:

* [Armazenamento físico](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Banco de dados](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo. O streaming não melhora significativamente o desempenho. O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.

O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico

Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.

O exemplo a seguir demonstra o uso de um Razor formulário de páginas para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:

* O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.
* Não há validação.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:

* Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).
* Use `XMLHttpRequest`. Por exemplo:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação ( `enctype` ) de `multipart/form-data` .

Para um `files` elemento input oferecer suporte ao carregamento de vários arquivos, forneça o `multiple` atributo no `<input>` elemento:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile> . O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.

<a name="filename"></a>

> [!WARNING]
> **Não** use a `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> outros para exibição e registro em log. Ao exibir ou registrar em log, o HTML codifica o nome do arquivo. Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos. Os aplicativos devem:
>
> * Remova o caminho do nome de arquivo fornecido pelo usuário.
> * Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.
> * Gere um novo nome de arquivo aleatório para armazenamento.
>
> O código a seguir remove o caminho do nome do arquivo:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança. Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile> o método de ação pode aceitar:

* Um único <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Qualquer uma das seguintes coleções que representam vários arquivos:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> A Associação corresponde aos arquivos de formulário por nome. Por exemplo, o `name` valor HTML in `<input type="file" name="formFile">` deve corresponder ao parâmetro/Propriedade do C# associado ( `FormFile` ). Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .

O exemplo a seguir:

* Executa um loop em um ou mais arquivos carregados.
* Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo. 
* Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.
* Retorna o número total e o tamanho dos arquivos carregados.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho. No exemplo a seguir, o caminho é obtido da configuração:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo. Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado no tempo de execução.

Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são armazenados em buffer na memória ou no disco no servidor antes do processamento. Dentro do método de ação, o <xref:Microsoft.AspNetCore.Http.IFormFile> conteúdo é acessível como um <xref:System.IO.Stream> . Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores. O limite de 65.535 arquivos é um limite por servidor. Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:
>
> * [Função GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados

Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma <xref:System.Byte> propriedade de matriz na entidade:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile> :

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada. O exemplo anterior usa uma propriedade de modelo associada.

O `FileUpload` é usado no Razor formulário páginas:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> para um fluxo e salve-o como uma matriz de bytes no banco de dados. No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:

* *Páginas/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.
>
> Não confie ou confie na `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação. A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.
>
> Os exemplos fornecidos não levam em consideração as considerações de segurança. Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

### <a name="upload-large-files-with-streaming"></a>Carregar arquivos grandes com streaming

O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador. O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação. Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado. Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado. Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.

A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do `GenerateAntiforgeryTokenCookieAttribute` atributo). O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página do `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando convenções de [ Razor páginas](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar). O método de ação funciona diretamente com a `Request` propriedade. Um `MultipartReader` é usado para ler cada seção. Os dados de chave/valor são armazenados em um `KeyValueAccumulator` . Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.

O `StreamingController.UploadDatabase` método completo para streaming para um banco de dados com EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

O `StreamingController.UploadPhysical` método completo para streaming para um local físico:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

No aplicativo de exemplo, as verificações de validação são tratadas pelo `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Validação

A classe do aplicativo de exemplo `FileHelpers` demonstra várias verificações para <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivos em buffer e em fluxo. Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o `ProcessFormFile` método no arquivo *Utilities/FileHelpers. cs* . Para o processamento de arquivos transmitidos, consulte o `ProcessStreamedFile` método no mesmo arquivo.

> [!WARNING]
> Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados. Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.
>
> Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a `FileHelpers` classe em um aplicativo de produção, a menos que você:
>
> * Entenda totalmente a implementação.
> * Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.
>
> **Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**

### <a name="content-validation"></a>Validação de conteúdo

**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**

A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume. Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo. Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique. Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal. Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo. Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.

### <a name="file-extension-validation"></a>Validação de extensão de arquivo

A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas. Por exemplo:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validação de assinatura de arquivo

A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo. Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo. O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns. No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.

### <a name="file-name-security"></a>Segurança de nome de arquivo

Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico. Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.

Razor o HTML automaticamente codifica valores de propriedade para exibição. O código a seguir é seguro para usar:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Fora do Razor , sempre <xref:System.Net.WebUtility.HtmlEncode*> nome do arquivo o conteúdo da solicitação de um usuário.

Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome. Forneça lógica adicional para atender às especificações do seu aplicativo.

### <a name="size-validation"></a>Validação de tamanho

Limite o tamanho dos arquivos carregados.

No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes). O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do *appsettings.json* arquivo:

```json
{
  "FileSizeLimit": 2097152
}
```

O `FileSizeLimit` é injetado em `PageModel` classes:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Coincidir valor do atributo de nome com o nome do parâmetro do método POST

Em não Razor formulários que postam dados de formulário ou usam o JavaScript `FormData` diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.

No exemplo a seguir:

* Ao usar um `<input>` elemento, o `name` atributo é definido como o valor `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Use um nome correspondente para o parâmetro do método C# ( `battlePlans` ):

* Para um Razor método de manipulador de página de páginas chamado `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Para um método de ação do controlador de POSTAgem MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuração de servidor e aplicativo

### <a name="multipart-body-length-limit"></a>Limite de comprimento de corpo com várias partes

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes. As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisado. O padrão é 134.217.728 (128 MB). Personalize o limite usando a <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> configuração em `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.

Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Tamanho máximo do corpo de solicitação do Kestrel

Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB. Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) para uma única página ou ação.

Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

O `RequestSizeLimitAttribute` também pode ser aplicado usando a [`@attribute`](xref:mvc/views/razor#attribute) Razor diretiva:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Outros limites de Kestrel

Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:

* [Número máximo de conexões de cliente](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [Taxas de dados de solicitação e resposta](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

O limite de solicitação padrão ( `maxAllowedContentLength` ) é 30 milhões bytes, que é aproximadamente 28,6 MB. Personalize o limite no `web.config` arquivo. No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

A `maxAllowedContentLength` configuração se aplica somente ao IIS. Para obter mais informações, consulte [limites `<requestLimits>` de solicitação ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

## <a name="troubleshoot"></a>Solucionar problemas

Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erro não encontrado quando implantado em um servidor IIS

O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Para obter mais informações, consulte a seção [IIS](#iis) .

### <a name="connection-failure"></a>Falha de conexão

Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel. Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) . Os limites de conexão de cliente Kestrel também podem exigir ajuste.

### <a name="null-reference-exception-with-iformfile"></a>Exceção de referência nula com IFormFile

Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile> , mas o valor for `null` , confirme se o formulário HTML está especificando um `enctype` valor de `multipart/form-data` . Se esse atributo não estiver definido no `<form>` elemento, o upload do arquivo não ocorrerá e todos os <xref:Microsoft.AspNetCore.Http.IFormFile> argumentos associados serão `null` . Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>O fluxo era muito longo

Os exemplos neste tópico dependem de <xref:System.IO.MemoryStream> manter o conteúdo do arquivo carregado. O limite de tamanho de um `MemoryStream` é `int.MaxValue` . Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa que não dependa de um único `MemoryStream` para manter o conteúdo de um arquivo carregado.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considerações de segurança

Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor. Os invasores podem tentar:

* Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .
* Carregar vírus ou malware.
* Comprometa redes e servidores de outras maneiras.

As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:

* Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema. Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados. Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;
* **Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;
* Use um nome de arquivo seguro determinado pelo aplicativo. Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; O HTML codifica o nome de arquivo não confiável ao exibi-lo. Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (a Razor saída de código HTML automaticamente).
* Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Verifique se as verificações do lado do cliente são executadas no servidor. &dagger; As verificações no lado do cliente são fáceis de evitar.
* Verifique o tamanho de um arquivo carregado. Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;
* Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.
* **Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**

&dagger;O aplicativo de exemplo demonstra uma abordagem que atende aos critérios.

> [!WARNING]
> Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:
>
> * Assuma completamente o controle de um sistema.
> * Sobrecarregar um sistema com o resultado que o sistema falha.
> * Comprometer dados do sistema ou de usuários.
> * Aplique graffiti a uma interface do usuário pública.
>
> Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
> * [Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .

## <a name="storage-scenarios"></a>Cenários de armazenamento

As opções de armazenamento comuns para arquivos incluem:

* Banco de dados

  * Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).
  * Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).
  * Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.

* Armazenamento físico (sistema de arquivos ou compartilhamento de rede)

  * Para carregamentos de arquivos grandes:
    * Os limites de banco de dados podem restringir o tamanho do carregamento.
    * O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.
  * O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.
  * O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento. **Nunca conceda a permissão EXECUTE.**

* Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))

  * Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.
  * Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.

  Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Cenários de upload de arquivo

Duas abordagens gerais para carregar arquivos são buffer e streaming.

**de resposta**

O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile> , que é uma representação em C# do arquivo usado para processar ou salvar o arquivo.

Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos. Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco. Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.

> [!NOTE]
> Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.

O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:

* [Armazenamento físico](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Banco de dados](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo. O streaming não melhora significativamente o desempenho. O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.

O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico

Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.

O exemplo a seguir demonstra o uso de um Razor formulário de páginas para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:

* O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.
* Não há validação.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:

* Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).
* Use `XMLHttpRequest`. Por exemplo:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação ( `enctype` ) de `multipart/form-data` .

Para um `files` elemento input oferecer suporte ao carregamento de vários arquivos, forneça o `multiple` atributo no `<input>` elemento:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile> . O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.

<a name="filename"></a>

> [!WARNING]
> **Não** use a `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> outros para exibição e registro em log. Ao exibir ou registrar em log, o HTML codifica o nome do arquivo. Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos. Os aplicativos devem:
>
> * Remova o caminho do nome de arquivo fornecido pelo usuário.
> * Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.
> * Gere um novo nome de arquivo aleatório para armazenamento.
>
> O código a seguir remove o caminho do nome do arquivo:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança. Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile> o método de ação pode aceitar:

* Um único <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Qualquer uma das seguintes coleções que representam vários arquivos:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> A Associação corresponde aos arquivos de formulário por nome. Por exemplo, o `name` valor HTML in `<input type="file" name="formFile">` deve corresponder ao parâmetro/Propriedade do C# associado ( `FormFile` ). Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .

O exemplo a seguir:

* Executa um loop em um ou mais arquivos carregados.
* Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo. 
* Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.
* Retorna o número total e o tamanho dos arquivos carregados.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho. No exemplo a seguir, o caminho é obtido da configuração:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo. Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado no tempo de execução.

Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são armazenados em buffer na memória ou no disco no servidor antes do processamento. Dentro do método de ação, o <xref:Microsoft.AspNetCore.Http.IFormFile> conteúdo é acessível como um <xref:System.IO.Stream> . Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores. O limite de 65.535 arquivos é um limite por servidor. Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:
>
> * [Função GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados

Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma <xref:System.Byte> propriedade de matriz na entidade:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile> :

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada. O exemplo anterior usa uma propriedade de modelo associada.

O `FileUpload` é usado no Razor formulário páginas:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> para um fluxo e salve-o como uma matriz de bytes no banco de dados. No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:

* *Páginas/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.
>
> Não confie ou confie na `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação. A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.
>
> Os exemplos fornecidos não levam em consideração as considerações de segurança. Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

### <a name="upload-large-files-with-streaming"></a>Carregar arquivos grandes com streaming

O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador. O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação. Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado. Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado. Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.

A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do `GenerateAntiforgeryTokenCookieAttribute` atributo). O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página do `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando convenções de [ Razor páginas](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar). O método de ação funciona diretamente com a `Request` propriedade. Um `MultipartReader` é usado para ler cada seção. Os dados de chave/valor são armazenados em um `KeyValueAccumulator` . Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.

O `StreamingController.UploadDatabase` método completo para streaming para um banco de dados com EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

O `StreamingController.UploadPhysical` método completo para streaming para um local físico:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

No aplicativo de exemplo, as verificações de validação são tratadas pelo `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Validação

A classe do aplicativo de exemplo `FileHelpers` demonstra várias verificações para <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivos em buffer e em fluxo. Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o `ProcessFormFile` método no arquivo *Utilities/FileHelpers. cs* . Para o processamento de arquivos transmitidos, consulte o `ProcessStreamedFile` método no mesmo arquivo.

> [!WARNING]
> Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados. Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.
>
> Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a `FileHelpers` classe em um aplicativo de produção, a menos que você:
>
> * Entenda totalmente a implementação.
> * Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.
>
> **Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**

### <a name="content-validation"></a>Validação de conteúdo

**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**

A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume. Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo. Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique. Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal. Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo. Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.

### <a name="file-extension-validation"></a>Validação de extensão de arquivo

A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas. Por exemplo:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validação de assinatura de arquivo

A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo. Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo. O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns. No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.

### <a name="file-name-security"></a>Segurança de nome de arquivo

Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico. Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.

Razor o HTML automaticamente codifica valores de propriedade para exibição. O código a seguir é seguro para usar:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Fora do Razor , sempre <xref:System.Net.WebUtility.HtmlEncode*> nome do arquivo o conteúdo da solicitação de um usuário.

Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome. Forneça lógica adicional para atender às especificações do seu aplicativo.

### <a name="size-validation"></a>Validação de tamanho

Limite o tamanho dos arquivos carregados.

No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes). O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do *appsettings.json* arquivo:

```json
{
  "FileSizeLimit": 2097152
}
```

O `FileSizeLimit` é injetado em `PageModel` classes:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Coincidir valor do atributo de nome com o nome do parâmetro do método POST

Em não Razor formulários que postam dados de formulário ou usam o JavaScript `FormData` diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.

No exemplo a seguir:

* Ao usar um `<input>` elemento, o `name` atributo é definido como o valor `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Use um nome correspondente para o parâmetro do método C# ( `battlePlans` ):

* Para um Razor método de manipulador de página de páginas chamado `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Para um método de ação do controlador de POSTAgem MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuração de servidor e aplicativo

### <a name="multipart-body-length-limit"></a>Limite de comprimento de corpo com várias partes

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes. As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisado. O padrão é 134.217.728 (128 MB). Personalize o limite usando a <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> configuração em `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.

Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Tamanho máximo do corpo de solicitação do Kestrel

Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB. Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.

Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

O `RequestSizeLimitAttribute` também pode ser aplicado usando a [`@attribute`](xref:mvc/views/razor#attribute) Razor diretiva:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Outros limites de Kestrel

Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:

* [Número máximo de conexões de cliente](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Taxas de dados de solicitação e resposta](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

O limite de solicitação padrão ( `maxAllowedContentLength` ) é 30 milhões bytes, que é aproximadamente 28,6 MB. Personalize o limite no `web.config` arquivo. No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

A `maxAllowedContentLength` configuração se aplica somente ao IIS. Para obter mais informações, consulte [limites `<requestLimits>` de solicitação ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Aumente o tamanho máximo do corpo de solicitação para a solicitação HTTP definindo <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> em `Startup.ConfigureServices` . No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Para obter mais informações, consulte <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Solucionar problemas

Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erro não encontrado quando implantado em um servidor IIS

O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Para obter mais informações, consulte a seção [IIS](#iis) .

### <a name="connection-failure"></a>Falha de conexão

Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel. Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) . Os limites de conexão de cliente Kestrel também podem exigir ajuste.

### <a name="null-reference-exception-with-iformfile"></a>Exceção de referência nula com IFormFile

Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile> , mas o valor for `null` , confirme se o formulário HTML está especificando um `enctype` valor de `multipart/form-data` . Se esse atributo não estiver definido no `<form>` elemento, o upload do arquivo não ocorrerá e todos os <xref:Microsoft.AspNetCore.Http.IFormFile> argumentos associados serão `null` . Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>O fluxo era muito longo

Os exemplos neste tópico dependem de <xref:System.IO.MemoryStream> manter o conteúdo do arquivo carregado. O limite de tamanho de um `MemoryStream` é `int.MaxValue` . Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa que não dependa de um único `MemoryStream` para manter o conteúdo de um arquivo carregado.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considerações de segurança

Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor. Os invasores podem tentar:

* Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .
* Carregar vírus ou malware.
* Comprometa redes e servidores de outras maneiras.

As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:

* Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema. Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados. Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;
* **Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;
* Use um nome de arquivo seguro determinado pelo aplicativo. Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; O HTML codifica o nome de arquivo não confiável ao exibi-lo. Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (a Razor saída de código HTML automaticamente).
* Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Verifique se as verificações do lado do cliente são executadas no servidor. &dagger; As verificações no lado do cliente são fáceis de evitar.
* Verifique o tamanho de um arquivo carregado. Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;
* Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.
* **Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**

&dagger;O aplicativo de exemplo demonstra uma abordagem que atende aos critérios.

> [!WARNING]
> Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:
>
> * Assuma completamente o controle de um sistema.
> * Sobrecarregar um sistema com o resultado que o sistema falha.
> * Comprometer dados do sistema ou de usuários.
> * Aplique graffiti a uma interface do usuário pública.
>
> Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
> * [Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .

## <a name="storage-scenarios"></a>Cenários de armazenamento

As opções de armazenamento comuns para arquivos incluem:

* Banco de dados

  * Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).
  * Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).
  * Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.

* Armazenamento físico (sistema de arquivos ou compartilhamento de rede)

  * Para carregamentos de arquivos grandes:
    * Os limites de banco de dados podem restringir o tamanho do carregamento.
    * O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.
  * O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.
  * O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento. **Nunca conceda a permissão EXECUTE.**

* Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))

  * Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.
  * Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.

  Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet). O tópico demonstra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode ser usado para salvar um <xref:System.IO.FileStream> armazenamento de BLOBs ao trabalhar com um <xref:System.IO.Stream> .

## <a name="file-upload-scenarios"></a>Cenários de upload de arquivo

Duas abordagens gerais para carregar arquivos são buffer e streaming.

**de resposta**

O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile> , que é uma representação em C# do arquivo usado para processar ou salvar o arquivo.

Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos. Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco. Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.

> [!NOTE]
> Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.

O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:

* [Armazenamento físico](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Banco de dados](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo. O streaming não melhora significativamente o desempenho. O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.

O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico

Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.

O exemplo a seguir demonstra o uso de um Razor formulário de páginas para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:

* O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.
* Não há validação.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:

* Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).
* Use `XMLHttpRequest`. Por exemplo:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação ( `enctype` ) de `multipart/form-data` .

Para um `files` elemento input oferecer suporte ao carregamento de vários arquivos, forneça o `multiple` atributo no `<input>` elemento:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile> . O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.

<a name="filename2"></a>

> [!WARNING]
> **Não** use a `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> outros para exibição e registro em log. Ao exibir ou registrar em log, o HTML codifica o nome do arquivo. Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos. Os aplicativos devem:
>
> * Remova o caminho do nome de arquivo fornecido pelo usuário.
> * Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.
> * Gere um novo nome de arquivo aleatório para armazenamento.
>
> O código a seguir remove o caminho do nome do arquivo:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança. Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile> o método de ação pode aceitar:

* Um único <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Qualquer uma das seguintes coleções que representam vários arquivos:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> A Associação corresponde aos arquivos de formulário por nome. Por exemplo, o `name` valor HTML in `<input type="file" name="formFile">` deve corresponder ao parâmetro/Propriedade do C# associado ( `FormFile` ). Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .

O exemplo a seguir:

* Executa um loop em um ou mais arquivos carregados.
* Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo. 
* Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.
* Retorna o número total e o tamanho dos arquivos carregados.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho. No exemplo a seguir, o caminho é obtido da configuração:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo. Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado no tempo de execução.

Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são armazenados em buffer na memória ou no disco no servidor antes do processamento. Dentro do método de ação, o <xref:Microsoft.AspNetCore.Http.IFormFile> conteúdo é acessível como um <xref:System.IO.Stream> . Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores. O limite de 65.535 arquivos é um limite por servidor. Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:
>
> * [Função GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados

Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma <xref:System.Byte> propriedade de matriz na entidade:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile> :

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada. O exemplo anterior usa uma propriedade de modelo associada.

O `FileUpload` é usado no Razor formulário páginas:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> para um fluxo e salve-o como uma matriz de bytes no banco de dados. No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:

* *Páginas/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.
>
> Não confie ou confie na `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação. A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.
>
> Os exemplos fornecidos não levam em consideração as considerações de segurança. Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

### <a name="upload-large-files-with-streaming"></a>Carregar arquivos grandes com streaming

O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador. O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação. Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado. Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado. Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.

A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do `GenerateAntiforgeryTokenCookieAttribute` atributo). O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página do `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando convenções de [ Razor páginas](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar). O método de ação funciona diretamente com a `Request` propriedade. Um `MultipartReader` é usado para ler cada seção. Os dados de chave/valor são armazenados em um `KeyValueAccumulator` . Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.

O `StreamingController.UploadDatabase` método completo para streaming para um banco de dados com EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

O `StreamingController.UploadPhysical` método completo para streaming para um local físico:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

No aplicativo de exemplo, as verificações de validação são tratadas pelo `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Validação

A classe do aplicativo de exemplo `FileHelpers` demonstra várias verificações para <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivos em buffer e em fluxo. Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o `ProcessFormFile` método no arquivo *Utilities/FileHelpers. cs* . Para o processamento de arquivos transmitidos, consulte o `ProcessStreamedFile` método no mesmo arquivo.

> [!WARNING]
> Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados. Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.
>
> Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a `FileHelpers` classe em um aplicativo de produção, a menos que você:
>
> * Entenda totalmente a implementação.
> * Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.
>
> **Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**

### <a name="content-validation"></a>Validação de conteúdo

**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**

A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume. Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo. Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique. Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal. Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo. Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.

### <a name="file-extension-validation"></a>Validação de extensão de arquivo

A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas. Por exemplo:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validação de assinatura de arquivo

A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo. Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo. O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns. No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.

### <a name="file-name-security"></a>Segurança de nome de arquivo

Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico. Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.

Razor o HTML automaticamente codifica valores de propriedade para exibição. O código a seguir é seguro para usar:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Fora do Razor , sempre <xref:System.Net.WebUtility.HtmlEncode*> nome do arquivo o conteúdo da solicitação de um usuário.

Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome. Forneça lógica adicional para atender às especificações do seu aplicativo.

### <a name="size-validation"></a>Validação de tamanho

Limite o tamanho dos arquivos carregados.

No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes). O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do *appsettings.json* arquivo:

```json
{
  "FileSizeLimit": 2097152
}
```

O `FileSizeLimit` é injetado em `PageModel` classes:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Coincidir valor do atributo de nome com o nome do parâmetro do método POST

Em não Razor formulários que postam dados de formulário ou usam o JavaScript `FormData` diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.

No exemplo a seguir:

* Ao usar um `<input>` elemento, o `name` atributo é definido como o valor `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Use um nome correspondente para o parâmetro do método C# ( `battlePlans` ):

* Para um Razor método de manipulador de página de páginas chamado `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Para um método de ação do controlador de POSTAgem MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuração de servidor e aplicativo

### <a name="multipart-body-length-limit"></a>Limite de comprimento de corpo com várias partes

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes. As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisado. O padrão é 134.217.728 (128 MB). Personalize o limite usando a <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> configuração em `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.

Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Tamanho máximo do corpo de solicitação do Kestrel

Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB. Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.

Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Outros limites de Kestrel

Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:

* [Número máximo de conexões de cliente](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Taxas de dados de solicitação e resposta](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

O limite de solicitação padrão ( `maxAllowedContentLength` ) é 30 milhões bytes, que é aproximadamente 28,6 MB. Personalize o limite no `web.config` arquivo. No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

A `maxAllowedContentLength` configuração se aplica somente ao IIS. Para obter mais informações, consulte [limites `<requestLimits>` de solicitação ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Aumente o tamanho máximo do corpo de solicitação para a solicitação HTTP definindo <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> em `Startup.ConfigureServices` . No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Para obter mais informações, consulte <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Solucionar problemas

Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erro não encontrado quando implantado em um servidor IIS

O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Para obter mais informações, consulte a seção [IIS](#iis) .

### <a name="connection-failure"></a>Falha de conexão

Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel. Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) . Os limites de conexão de cliente Kestrel também podem exigir ajuste.

### <a name="null-reference-exception-with-iformfile"></a>Exceção de referência nula com IFormFile

Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile> , mas o valor for `null` , confirme se o formulário HTML está especificando um `enctype` valor de `multipart/form-data` . Se esse atributo não estiver definido no `<form>` elemento, o upload do arquivo não ocorrerá e todos os <xref:Microsoft.AspNetCore.Http.IFormFile> argumentos associados serão `null` . Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>O fluxo era muito longo

Os exemplos neste tópico dependem de <xref:System.IO.MemoryStream> manter o conteúdo do arquivo carregado. O limite de tamanho de um `MemoryStream` é `int.MaxValue` . Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa que não dependa de um único `MemoryStream` para manter o conteúdo de um arquivo carregado.

::: moniker-end


## <a name="additional-resources"></a>Recursos adicionais

::: moniker range="< aspnetcore-5.0"
* [Descarga de solicitação de conexão HTTP](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [Descarga de solicitação de conexão HTTP](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
* [Segurança do Azure: estrutura de segurança: validação de entrada | Mitigações](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Padrões de design de nuvem do Azure: padrão valet Key](/azure/architecture/patterns/valet-key)
