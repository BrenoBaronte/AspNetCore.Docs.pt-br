---
title: ASP.NET Core Blazor carregamentos de arquivos
author: guardrex
description: Saiba como carregar arquivos no Blazor com o componente InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: 77c2874eef788b8083758c087913a7a04c55fa2b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94691164"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="9a90e-103">ASP.NET Core Blazor carregamentos de arquivos</span><span class="sxs-lookup"><span data-stu-id="9a90e-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="9a90e-104">Por [Daniel Roth](https://github.com/danroth27) e [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="9a90e-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="9a90e-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a90e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9a90e-106">Use o `InputFile` componente para ler os dados do arquivo de navegador no código .net, incluindo para carregamentos de arquivos.</span><span class="sxs-lookup"><span data-stu-id="9a90e-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="9a90e-107">Sempre siga as práticas recomendadas de segurança de carregamento de arquivo.</span><span class="sxs-lookup"><span data-stu-id="9a90e-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="9a90e-108">Para obter mais informações, consulte <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="9a90e-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="9a90e-109">componente `InputFile`</span><span class="sxs-lookup"><span data-stu-id="9a90e-109">`InputFile` component</span></span>

<span data-ttu-id="9a90e-110">O `InputFile` componente é renderizado como uma entrada HTML do tipo `file` .</span><span class="sxs-lookup"><span data-stu-id="9a90e-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="9a90e-111">Por padrão, o usuário seleciona arquivos únicos.</span><span class="sxs-lookup"><span data-stu-id="9a90e-111">By default, the user selects single files.</span></span> <span data-ttu-id="9a90e-112">Adicione o `multiple` atributo para permitir que o usuário carregue vários arquivos ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="9a90e-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="9a90e-113">Quando um ou mais arquivos é selecionado pelo usuário, o `InputFile` componente dispara um `OnChange` evento e passa um `InputFileChangeEventArgs` que fornece acesso à lista de arquivos selecionada e aos detalhes de cada arquivo.</span><span class="sxs-lookup"><span data-stu-id="9a90e-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="9a90e-114">Para ler dados de um arquivo selecionado pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="9a90e-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="9a90e-115">Chame `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` no arquivo e leia a partir do fluxo retornado.</span><span class="sxs-lookup"><span data-stu-id="9a90e-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="9a90e-116">Para obter mais informações, consulte a seção [fluxos de arquivos](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="9a90e-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="9a90e-117">O <xref:System.IO.Stream> retornado por `OpenReadStream` impõe um tamanho máximo em bytes do `Stream` que está sendo lido.</span><span class="sxs-lookup"><span data-stu-id="9a90e-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="9a90e-118">Por padrão, os arquivos com tamanho superior a 512.000 bytes (500 KB) podem ser lidos antes que qualquer outra leitura resulte em uma exceção.</span><span class="sxs-lookup"><span data-stu-id="9a90e-118">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="9a90e-119">Esse limite está presente para impedir que os desenvolvedores leiam acidentalmente arquivos grandes na memória.</span><span class="sxs-lookup"><span data-stu-id="9a90e-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="9a90e-120">O `maxAllowedSize` parâmetro on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` pode ser usado para especificar um tamanho maior, se necessário.</span><span class="sxs-lookup"><span data-stu-id="9a90e-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="9a90e-121">Evite ler o fluxo de arquivos de entrada diretamente na memória.</span><span class="sxs-lookup"><span data-stu-id="9a90e-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="9a90e-122">Por exemplo, não copie bytes de arquivo em um <xref:System.IO.MemoryStream> ou leia como uma matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="9a90e-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="9a90e-123">Essas abordagens podem resultar em problemas de desempenho e segurança, especialmente no Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="9a90e-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="9a90e-124">Em vez disso, considere copiar bytes de arquivo para um repositório externo, como um BLOB ou um arquivo no disco.</span><span class="sxs-lookup"><span data-stu-id="9a90e-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="9a90e-125">Um componente que recebe um arquivo de imagem pode chamar o `RequestImageFileAsync` método de conveniência no arquivo para redimensionar os dados da imagem dentro do tempo de execução do JavaScript do navegador antes que a imagem seja transmitida para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a90e-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="9a90e-126">O exemplo a seguir demonstra o carregamento de vários arquivos de imagem em um componente.</span><span class="sxs-lookup"><span data-stu-id="9a90e-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="9a90e-127">`InputFileChangeEventArgs.GetMultipleFiles` permite ler vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="9a90e-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="9a90e-128">Especifique o número máximo de arquivos que você pretende ler para impedir que um usuário mal-intencionado carregue um número maior de arquivos do que o aplicativo espera.</span><span class="sxs-lookup"><span data-stu-id="9a90e-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="9a90e-129">`InputFileChangeEventArgs.File` permite a leitura do primeiro e único arquivo se o upload do arquivo não oferecer suporte a vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="9a90e-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

> [!NOTE]
> <span data-ttu-id="9a90e-130"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> está no <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> namespace, que normalmente é um dos namespaces no arquivo do aplicativo `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="9a90e-130"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> is in the <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> namespace, which is typically one of the namespaces in the app's `_Imports.razor` file.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    private IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="9a90e-131">`IBrowserFile` retorna [os metadados expostos pelo navegador](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) como propriedades.</span><span class="sxs-lookup"><span data-stu-id="9a90e-131">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="9a90e-132">Esses metadados podem ser úteis para a validação preliminar.</span><span class="sxs-lookup"><span data-stu-id="9a90e-132">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="9a90e-133">Por exemplo, consulte os [ `FileUpload.razor` `FilePreview.razor` componentes de exemplo e](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="9a90e-133">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="9a90e-134">Fluxos de arquivos</span><span class="sxs-lookup"><span data-stu-id="9a90e-134">File streams</span></span>

<span data-ttu-id="9a90e-135">Em um Blazor WebAssembly aplicativo, os dados são transmitidos diretamente para o código .net dentro do navegador.</span><span class="sxs-lookup"><span data-stu-id="9a90e-135">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="9a90e-136">Em um Blazor Server aplicativo, os dados de arquivo são transmitidos pela SignalR conexão no código .net no servidor, pois o arquivo é lido no fluxo.</span><span class="sxs-lookup"><span data-stu-id="9a90e-136">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="9a90e-137">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) permite configurar características de upload de arquivo para Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="9a90e-137">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a90e-138">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9a90e-138">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
