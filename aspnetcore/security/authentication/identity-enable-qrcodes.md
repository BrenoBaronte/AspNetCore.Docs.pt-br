---
title: Habilitar a geração de código QR TOTP para aplicativos de autenticador no ASP.NET Core
author: rick-anderson
description: Descubra como habilitar a geração de código QR para aplicativos de autenticador TOTP que funcionam com autenticação de dois fatores de núcleo do ASP.NET.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 437f354f71128a98bae9abdced291e04efc9f48e
ms.sourcegitcommit: fc7eb4243188950ae1f1b52669edc007e9d0798d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51225376"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="e8452-103">Habilitar a geração de código QR TOTP para aplicativos de autenticador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8452-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="e8452-104">Códigos QR exige o ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="e8452-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e8452-105">ASP.NET Core é fornecido com suporte para aplicativos de autenticador para autenticação individual.</span><span class="sxs-lookup"><span data-stu-id="e8452-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="e8452-106">Dois aplicativos de autenticador 2FA (autenticação) fator, usando uma baseada em tempo avulso senha algoritmo TOTP (), são o setor de abordagem 2fa recomendado.</span><span class="sxs-lookup"><span data-stu-id="e8452-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="e8452-107">2FA usar TOTP é preferencial para SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="e8452-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="e8452-108">Um aplicativo autenticador fornece um código de 6 a 8 dígitos que os usuários devem inserir depois de confirmar seu nome de usuário e senha.</span><span class="sxs-lookup"><span data-stu-id="e8452-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="e8452-109">Normalmente, um aplicativo autenticador é instalado em um Smartphone.</span><span class="sxs-lookup"><span data-stu-id="e8452-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="e8452-110">Os modelos de aplicativo web ASP.NET Core autenticadores de suporte, mas não fornecem suporte para a geração de QRCode.</span><span class="sxs-lookup"><span data-stu-id="e8452-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="e8452-111">Geradores de QRCode facilitam a configuração do 2FA.</span><span class="sxs-lookup"><span data-stu-id="e8452-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="e8452-112">Este documento o orientará durante a adição [código QR](https://wikipedia.org/wiki/QR_code) geração para a página de configuração 2FA.</span><span class="sxs-lookup"><span data-stu-id="e8452-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="e8452-113">Adicionar códigos QR para a página de configuração 2FA</span><span class="sxs-lookup"><span data-stu-id="e8452-113">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="e8452-114">Usam estas instruções *qrcode.js* do https://davidshimjs.github.io/qrcodejs/ repositório.</span><span class="sxs-lookup"><span data-stu-id="e8452-114">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="e8452-115">Baixe o [biblioteca de javascript qrcode.js](https://davidshimjs.github.io/qrcodejs/) para o `wwwroot\lib` pasta em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="e8452-115">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="e8452-116">Siga as instruções em [identidade de Scaffold](xref:security/authentication/scaffold-identity) para gerar */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e8452-116">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="e8452-117">Na */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, localize o `Scripts` seção no final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="e8452-117">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="e8452-118">Na *Pages/Account/Manage/EnableAuthenticator.cshtml* (páginas do Razor) ou *Views/Manage/EnableAuthenticator.cshtml* (MVC), localize o `Scripts` seção no final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="e8452-118">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="e8452-119">Atualizar o `Scripts` seção para adicionar uma referência para o `qrcodejs` biblioteca que você adicionou e uma chamada para gerar o código QR.</span><span class="sxs-lookup"><span data-stu-id="e8452-119">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="e8452-120">Ele deve ser da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="e8452-120">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="e8452-121">Exclua o parágrafo que links para essas instruções.</span><span class="sxs-lookup"><span data-stu-id="e8452-121">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="e8452-122">Executar seu aplicativo e certifique-se de que você pode digitalizar o código QR e validar o código que comprova o autenticador.</span><span class="sxs-lookup"><span data-stu-id="e8452-122">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="e8452-123">Alterar o nome do site em que o código QR</span><span class="sxs-lookup"><span data-stu-id="e8452-123">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e8452-124">O nome do site em que o código QR é obtido do nome do projeto que você escolher ao criar inicialmente o seu projeto.</span><span class="sxs-lookup"><span data-stu-id="e8452-124">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="e8452-125">Você pode alterá-lo procurando os `GenerateQrCodeUri(string email, string unformattedKey)` método na */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e8452-125">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e8452-126">O nome do site em que o código QR é obtido do nome do projeto que você escolher ao criar inicialmente o seu projeto.</span><span class="sxs-lookup"><span data-stu-id="e8452-126">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="e8452-127">Você pode alterá-lo procurando a `GenerateQrCodeUri(string email, string unformattedKey)` método na *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* arquivo (páginas do Razor) ou o *Controllers/ManageController.cs* arquivo (MVC).</span><span class="sxs-lookup"><span data-stu-id="e8452-127">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e8452-128">O código padrão do modelo será semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="e8452-128">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="e8452-129">O segundo parâmetro na chamada para `string.Format` é o nome do site, obtido do nome da solução.</span><span class="sxs-lookup"><span data-stu-id="e8452-129">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="e8452-130">Ele pode ser alterado para qualquer valor, mas ele sempre deve ser codificado por URL.</span><span class="sxs-lookup"><span data-stu-id="e8452-130">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="e8452-131">Usando uma biblioteca diferente do código QR</span><span class="sxs-lookup"><span data-stu-id="e8452-131">Using a different QR Code library</span></span>

<span data-ttu-id="e8452-132">Você pode substituir a biblioteca de código QR com sua biblioteca preferencial.</span><span class="sxs-lookup"><span data-stu-id="e8452-132">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="e8452-133">O HTML não contém um `qrCode` fornece de sua biblioteca de elemento no qual você pode colocar um código QR por qualquer mecanismo.</span><span class="sxs-lookup"><span data-stu-id="e8452-133">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="e8452-134">A URL formatada corretamente para o código QR está disponível na:</span><span class="sxs-lookup"><span data-stu-id="e8452-134">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="e8452-135">`AuthenticatorUri` propriedade do modelo.</span><span class="sxs-lookup"><span data-stu-id="e8452-135">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="e8452-136">`data-url` propriedade no `qrCodeData` elemento.</span><span class="sxs-lookup"><span data-stu-id="e8452-136">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="e8452-137">TOTP cliente e servidor distorção de tempo</span><span class="sxs-lookup"><span data-stu-id="e8452-137">TOTP client and server time skew</span></span>

<span data-ttu-id="e8452-138">Autenticação de TOTP (senha de uso único baseados em tempo) depende do dispositivo o servidor e o autenticador, tendo a hora correta.</span><span class="sxs-lookup"><span data-stu-id="e8452-138">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="e8452-139">Tokens duram por 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="e8452-139">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="e8452-140">Se os logons de 2FA TOTP estiverem falhando, verifique se a hora do servidor é precisos e preferencialmente sincronizado a um serviço NTP preciso.</span><span class="sxs-lookup"><span data-stu-id="e8452-140">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
