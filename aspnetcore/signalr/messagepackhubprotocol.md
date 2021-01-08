---
title: Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core
author: bradygaster
description: Adicione o protocolo Hub MessagePack a ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024685"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="ed0f9-103">Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ed0f9-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ed0f9-104">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="ed0f9-105">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="ed0f9-105">What is MessagePack?</span></span>

<span data-ttu-id="ed0f9-106">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="ed0f9-107">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="ed0f9-108">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="ed0f9-109">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="ed0f9-110">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="ed0f9-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="ed0f9-111">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="ed0f9-112">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-113">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-113">JSON is enabled by default.</span></span> <span data-ttu-id="ed0f9-114">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="ed0f9-115">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="ed0f9-116">Nesse delegado, a `SerializerOptions` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="ed0f9-117">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="ed0f9-118">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="ed0f9-119">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="ed0f9-120">Por exemplo, chamar `.WithSecurity(MessagePackSecurity.UntrustedData)` ao substituir o `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="ed0f9-121">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="ed0f9-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-122">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="ed0f9-123">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="ed0f9-124">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="ed0f9-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ed0f9-125">.NET client</span></span>

<span data-ttu-id="ed0f9-126">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="ed0f9-127">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="ed0f9-128">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-128">JavaScript client</span></span>

<span data-ttu-id="ed0f9-129">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="ed0f9-130">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="ed0f9-131">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="ed0f9-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="ed0f9-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="ed0f9-133">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="ed0f9-134">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="ed0f9-135">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-136">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="ed0f9-137">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="ed0f9-138">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="ed0f9-139">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="ed0f9-140">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="ed0f9-141">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="ed0f9-141">Java client</span></span>

<span data-ttu-id="ed0f9-142">Para habilitar o MessagePack com Java, instale o `com.microsoft.signalr.messagepack` pacote.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="ed0f9-143">Ao usar gradle, adicione a seguinte linha à `dependencies` seção do arquivo *Build. gradle* :</span><span class="sxs-lookup"><span data-stu-id="ed0f9-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="ed0f9-144">Ao usar o Maven, adicione as seguintes linhas dentro do `<dependencies>` elemento do arquivo de *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="ed0f9-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="ed0f9-145">Ligue `withHubProtocol(new MessagePackHubProtocol())` para `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="ed0f9-146">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="ed0f9-146">MessagePack quirks</span></span>

<span data-ttu-id="ed0f9-147">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="ed0f9-148">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="ed0f9-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="ed0f9-149">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="ed0f9-150">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="ed0f9-151">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="ed0f9-152">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="ed0f9-153">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="ed0f9-154">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="ed0f9-155">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="ed0f9-156">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="ed0f9-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="ed0f9-157">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="ed0f9-158">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack será convertido para o formato UTC se, `DateTime.Kind` `DateTimeKind.Local` caso contrário, ele não tocará o tempo e o passará como está.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="ed0f9-159">Se você estiver trabalhando com `DateTime` valores, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="ed0f9-160">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="ed0f9-161">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="ed0f9-162">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="ed0f9-163">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="ed0f9-164">O valor de `DateTime.MinValue` é `January 1, 0001` , que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="ed0f9-165">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="ed0f9-166">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="ed0f9-167">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="ed0f9-168">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="ed0f9-169">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="ed0f9-170">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="ed0f9-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="ed0f9-171">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="ed0f9-172">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="ed0f9-173">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="ed0f9-174">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="ed0f9-175">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="ed0f9-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="ed0f9-176">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="ed0f9-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="ed0f9-177">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="ed0f9-178">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="ed0f9-179">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="ed0f9-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="ed0f9-180">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="ed0f9-181">Chars e cadeias de caracteres em Java</span><span class="sxs-lookup"><span data-stu-id="ed0f9-181">Chars and Strings in Java</span></span>

<span data-ttu-id="ed0f9-182">No cliente Java, os `char` objetos serão serializados como objetos de um caractere `String` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="ed0f9-183">Isso está em contraste com o cliente C# e JavaScript, que os serializa como `short` objetos.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="ed0f9-184">A especificação de MessagePack em si não define o comportamento de `char` objetos, portanto, cabe ao autor da biblioteca determinar como serializá-los.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="ed0f9-185">A diferença no comportamento entre nossos clientes é um resultado das bibliotecas usadas para nossas implementações.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="ed0f9-186">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="ed0f9-186">Related resources</span></span>

* [<span data-ttu-id="ed0f9-187">Introdução</span><span class="sxs-lookup"><span data-stu-id="ed0f9-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ed0f9-188">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ed0f9-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ed0f9-189">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ed0f9-190">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="ed0f9-191">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="ed0f9-191">What is MessagePack?</span></span>

<span data-ttu-id="ed0f9-192">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="ed0f9-193">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="ed0f9-194">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="ed0f9-195">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="ed0f9-196">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="ed0f9-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="ed0f9-197">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="ed0f9-198">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-199">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-199">JSON is enabled by default.</span></span> <span data-ttu-id="ed0f9-200">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="ed0f9-201">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="ed0f9-202">Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="ed0f9-203">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="ed0f9-204">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="ed0f9-205">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="ed0f9-206">Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="ed0f9-207">Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="ed0f9-208">`MessagePack`A instalação das atualizações 1.9. x é usada pela versão SignalR .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="ed0f9-209">`MessagePack` a versão 2. x introduziu alterações significativas e é incompatível com SignalR as versões 3,1 e anteriores.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="ed0f9-210">Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData` , um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="ed0f9-211">Defina `MessagePackSecurity.Active` em `Program.Main` , conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="ed0f9-212">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="ed0f9-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-213">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="ed0f9-214">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="ed0f9-215">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="ed0f9-216">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ed0f9-216">.NET client</span></span>

<span data-ttu-id="ed0f9-217">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="ed0f9-218">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="ed0f9-219">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-219">JavaScript client</span></span>

<span data-ttu-id="ed0f9-220">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="ed0f9-221">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="ed0f9-222">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="ed0f9-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="ed0f9-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="ed0f9-224">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="ed0f9-225">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="ed0f9-226">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-227">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="ed0f9-228">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="ed0f9-229">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="ed0f9-230">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="ed0f9-231">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="ed0f9-232">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="ed0f9-232">MessagePack quirks</span></span>

<span data-ttu-id="ed0f9-233">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="ed0f9-234">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="ed0f9-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="ed0f9-235">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="ed0f9-236">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="ed0f9-237">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="ed0f9-238">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="ed0f9-239">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="ed0f9-240">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="ed0f9-241">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="ed0f9-242">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="ed0f9-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="ed0f9-243">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="ed0f9-244">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="ed0f9-245">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="ed0f9-246">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="ed0f9-247">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="ed0f9-248">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="ed0f9-249">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="ed0f9-250">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="ed0f9-251">O valor de `DateTime.MinValue` é `January 1, 0001` , que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="ed0f9-252">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="ed0f9-253">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="ed0f9-254">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="ed0f9-255">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="ed0f9-256">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="ed0f9-257">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="ed0f9-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="ed0f9-258">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="ed0f9-259">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="ed0f9-260">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="ed0f9-261">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="ed0f9-262">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="ed0f9-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="ed0f9-263">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="ed0f9-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="ed0f9-264">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="ed0f9-265">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="ed0f9-266">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="ed0f9-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="ed0f9-267">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="ed0f9-268">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="ed0f9-268">Related resources</span></span>

* [<span data-ttu-id="ed0f9-269">Introdução</span><span class="sxs-lookup"><span data-stu-id="ed0f9-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ed0f9-270">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ed0f9-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ed0f9-271">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ed0f9-272">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="ed0f9-273">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="ed0f9-273">What is MessagePack?</span></span>

<span data-ttu-id="ed0f9-274">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="ed0f9-275">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="ed0f9-276">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="ed0f9-277">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="ed0f9-278">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="ed0f9-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="ed0f9-279">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="ed0f9-280">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-281">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-281">JSON is enabled by default.</span></span> <span data-ttu-id="ed0f9-282">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="ed0f9-283">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="ed0f9-284">Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="ed0f9-285">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="ed0f9-286">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="ed0f9-287">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="ed0f9-288">Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="ed0f9-289">Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="ed0f9-290">`MessagePack`A instalação das atualizações 1.9. x é usada pela versão SignalR .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="ed0f9-291">Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData` , um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="ed0f9-292">Defina `MessagePackSecurity.Active` em `Program.Main` , conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="ed0f9-293">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="ed0f9-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-294">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="ed0f9-295">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="ed0f9-296">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="ed0f9-297">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ed0f9-297">.NET client</span></span>

<span data-ttu-id="ed0f9-298">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="ed0f9-299">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="ed0f9-300">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-300">JavaScript client</span></span>

<span data-ttu-id="ed0f9-301">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="ed0f9-302">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="ed0f9-303">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="ed0f9-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="ed0f9-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="ed0f9-305">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="ed0f9-306">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="ed0f9-307">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="ed0f9-308">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="ed0f9-309">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="ed0f9-310">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="ed0f9-311">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="ed0f9-312">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="ed0f9-313">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="ed0f9-313">MessagePack quirks</span></span>

<span data-ttu-id="ed0f9-314">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="ed0f9-315">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="ed0f9-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="ed0f9-316">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="ed0f9-317">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="ed0f9-318">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="ed0f9-319">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="ed0f9-320">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="ed0f9-321">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="ed0f9-322">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="ed0f9-323">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="ed0f9-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="ed0f9-324">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="ed0f9-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="ed0f9-325">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="ed0f9-326">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="ed0f9-327">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="ed0f9-328">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="ed0f9-329">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="ed0f9-330">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="ed0f9-331">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="ed0f9-332">O valor de `DateTime.MinValue` é `January 1, 0001` que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="ed0f9-333">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="ed0f9-334">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="ed0f9-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="ed0f9-335">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="ed0f9-336">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="ed0f9-337">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="ed0f9-338">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="ed0f9-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="ed0f9-339">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="ed0f9-340">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="ed0f9-341">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="ed0f9-342">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="ed0f9-343">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="ed0f9-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="ed0f9-344">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="ed0f9-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="ed0f9-345">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="ed0f9-346">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="ed0f9-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="ed0f9-347">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="ed0f9-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="ed0f9-348">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="ed0f9-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="ed0f9-349">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="ed0f9-349">Related resources</span></span>

* [<span data-ttu-id="ed0f9-350">Introdução</span><span class="sxs-lookup"><span data-stu-id="ed0f9-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ed0f9-351">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ed0f9-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ed0f9-352">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="ed0f9-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
