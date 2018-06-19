---
uid: web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
title: Use OWIN para hospedar o ASP.NET Web API 2 | Microsoft Docs
author: rick-anderson
description: Este tutorial mostra como hospedar API Web do ASP.NET em um aplicativo de console, usando OWIN para a estrutura da API Web de hospedagem interna. Abra a Interface da Web para .NET (OWIN) d...
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/09/2013
ms.topic: article
ms.assetid: a90a04ce-9d07-43ad-8250-8a92fb2bd3d5
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
msc.type: authoredcontent
ms.openlocfilehash: fda0db8155c3303907331a690af35f619b589154
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
ms.locfileid: "26506965"
---
<a name="use-owin-to-self-host-aspnet-web-api-2"></a><span data-ttu-id="a86e7-104">Use OWIN para hospedar o ASP.NET Web API 2</span><span class="sxs-lookup"><span data-stu-id="a86e7-104">Use OWIN to Self-Host ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="a86e7-105">por [Kanchan Mehrotra](https://twitter.com/kanchanmeh)</span><span class="sxs-lookup"><span data-stu-id="a86e7-105">by [Kanchan Mehrotra](https://twitter.com/kanchanmeh)</span></span>

> <span data-ttu-id="a86e7-106">Este tutorial mostra como hospedar API Web do ASP.NET em um aplicativo de console, usando OWIN para a estrutura da API Web de hospedagem interna.</span><span class="sxs-lookup"><span data-stu-id="a86e7-106">This tutorial shows how to host ASP.NET Web API in a console application, using OWIN to self-host the Web API framework.</span></span>
> 
> <span data-ttu-id="a86e7-107">[Abra a Interface da Web para .NET](http://owin.org) (OWIN) define uma abstração entre os servidores de web do .NET e aplicativos da web.</span><span class="sxs-lookup"><span data-stu-id="a86e7-107">[Open Web Interface for .NET](http://owin.org) (OWIN) defines an abstraction between .NET web servers and web applications.</span></span> <span data-ttu-id="a86e7-108">OWIN separa o aplicativo web do servidor, o que torna OWIN ideal para hospedagem interna de um aplicativo da web em seu próprio processo e fora do IIS.</span><span class="sxs-lookup"><span data-stu-id="a86e7-108">OWIN decouples the web application from the server, which makes OWIN ideal for self-hosting a web application in your own process, outside of IIS.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="a86e7-109">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="a86e7-109">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="a86e7-110">[Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads) (também funciona com o Visual Studio 2012)</span><span class="sxs-lookup"><span data-stu-id="a86e7-110">[Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads) (also works with Visual Studio 2012)</span></span>
> - <span data-ttu-id="a86e7-111">Web API 2</span><span class="sxs-lookup"><span data-stu-id="a86e7-111">Web API 2</span></span>


> [!NOTE]
> <span data-ttu-id="a86e7-112">Você pode encontrar o código-fonte completo para este tutorial em [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).</span><span class="sxs-lookup"><span data-stu-id="a86e7-112">You can find the complete source code for this tutorial at [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).</span></span>


## <a name="create-a-console-application"></a><span data-ttu-id="a86e7-113">Criar um aplicativo de Console</span><span class="sxs-lookup"><span data-stu-id="a86e7-113">Create a Console Application</span></span>

<span data-ttu-id="a86e7-114">No **arquivo** menu, clique em **novo**, em seguida, clique em **projeto**.</span><span class="sxs-lookup"><span data-stu-id="a86e7-114">On the **File** menu, click **New**, then click **Project**.</span></span> <span data-ttu-id="a86e7-115">De **modelos instalados**, em Visual c#, clique em **Windows** e, em seguida, clique em **aplicativo de Console**.</span><span class="sxs-lookup"><span data-stu-id="a86e7-115">From **Installed Templates**, under Visual C#, click **Windows** and then click **Console Application**.</span></span> <span data-ttu-id="a86e7-116">Nomeie o projeto "OwinSelfhostSample" e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="a86e7-116">Name the project "OwinSelfhostSample" and click **OK**.</span></span>

[![](use-owin-to-self-host-web-api/_static/image2.png)](use-owin-to-self-host-web-api/_static/image1.png)

## <a name="add-the-web-api-and-owin-packages"></a><span data-ttu-id="a86e7-117">Adicionar a API da Web e pacotes OWIN</span><span class="sxs-lookup"><span data-stu-id="a86e7-117">Add the Web API and OWIN Packages</span></span>

<span data-ttu-id="a86e7-118">Do **ferramentas** menu, clique em **Gerenciador de biblioteca de pacote**, em seguida, clique em **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="a86e7-118">From the **Tools** menu, click **Library Package Manager**, then click **Package Manager Console**.</span></span> <span data-ttu-id="a86e7-119">Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a86e7-119">In the Package Manager Console window, enter the following command:</span></span>

`Install-Package Microsoft.AspNet.WebApi.OwinSelfHost`

<span data-ttu-id="a86e7-120">Isso instalará o pacote de selfhost WebAPI OWIN e todos os pacotes necessários do OWIN.</span><span class="sxs-lookup"><span data-stu-id="a86e7-120">This will install the WebAPI OWIN selfhost package and all the required OWIN packages.</span></span>

[![](use-owin-to-self-host-web-api/_static/image4.png)](use-owin-to-self-host-web-api/_static/image3.png)

## <a name="configure-web-api-for-self-host"></a><span data-ttu-id="a86e7-121">Configurar a API da Web para hospedar internamente</span><span class="sxs-lookup"><span data-stu-id="a86e7-121">Configure Web API for Self-Host</span></span>

<span data-ttu-id="a86e7-122">No Gerenciador de soluções, clique com botão direito no projeto e selecione **adicionar** / **classe** para adicionar uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="a86e7-122">In Solution Explorer, right click the project and select **Add** / **Class** to add a new class.</span></span> <span data-ttu-id="a86e7-123">Nomeie a classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="a86e7-123">Name the class `Startup`.</span></span>

![](use-owin-to-self-host-web-api/_static/image5.png)

<span data-ttu-id="a86e7-124">Substitua todo o código clichê nesse arquivo com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="a86e7-124">Replace all of the boilerplate code in this file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample1.cs)]

## <a name="add-a-web-api-controller"></a><span data-ttu-id="a86e7-125">Adicionar um controlador de API da Web</span><span class="sxs-lookup"><span data-stu-id="a86e7-125">Add a Web API Controller</span></span>

<span data-ttu-id="a86e7-126">Em seguida, adicione uma classe de controlador de API da Web.</span><span class="sxs-lookup"><span data-stu-id="a86e7-126">Next, add a Web API controller class.</span></span> <span data-ttu-id="a86e7-127">No Gerenciador de soluções, clique com botão direito no projeto e selecione **adicionar** / **classe** para adicionar uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="a86e7-127">In Solution Explorer, right click the project and select **Add** / **Class** to add a new class.</span></span> <span data-ttu-id="a86e7-128">Nomeie a classe `ValuesController`.</span><span class="sxs-lookup"><span data-stu-id="a86e7-128">Name the class `ValuesController`.</span></span>

<span data-ttu-id="a86e7-129">Substitua todo o código clichê nesse arquivo com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="a86e7-129">Replace all of the boilerplate code in this file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample2.cs)]

## <a name="start-the-owin-host-and-make-a-request-using-httpclient"></a><span data-ttu-id="a86e7-130">Iniciar o Host OWIN e fazer uma solicitação usando HttpClient</span><span class="sxs-lookup"><span data-stu-id="a86e7-130">Start the OWIN Host and Make a Request Using HttpClient</span></span>

<span data-ttu-id="a86e7-131">Substitua todo o código clichê no arquivo Program.cs pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="a86e7-131">Replace all of the boilerplate code in the Program.cs file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample3.cs)]

## <a name="running-the-application"></a><span data-ttu-id="a86e7-132">Executando o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a86e7-132">Running the Application</span></span>

<span data-ttu-id="a86e7-133">Para executar o aplicativo, pressione F5 no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a86e7-133">To run the application, press F5 in Visual Studio.</span></span> <span data-ttu-id="a86e7-134">A saída deve se parecer com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="a86e7-134">The output should look like the following:</span></span>

[!code-console[Main](use-owin-to-self-host-web-api/samples/sample4.cmd)]

![](use-owin-to-self-host-web-api/_static/image6.png)

## <a name="additional-resources"></a><span data-ttu-id="a86e7-135">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a86e7-135">Additional Resources</span></span>

[<span data-ttu-id="a86e7-136">Uma visão geral do projeto Katana</span><span class="sxs-lookup"><span data-stu-id="a86e7-136">An Overview of Project Katana</span></span>](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md)

[<span data-ttu-id="a86e7-137">Hospedar a API da Web ASP.NET em uma função de trabalho do Azure</span><span class="sxs-lookup"><span data-stu-id="a86e7-137">Host ASP.NET Web API in an Azure Worker Role</span></span>](host-aspnet-web-api-in-an-azure-worker-role.md)
