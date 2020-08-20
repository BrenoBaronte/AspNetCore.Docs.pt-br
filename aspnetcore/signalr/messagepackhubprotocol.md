---
title: Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core
author: bradygaster
description: Adicione o protocolo Hub MessagePack a ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
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
ms.openlocfilehash: ab9bd11e37182f5b24db5595d5d050f4cc0e32da
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626643"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="452ae-103">Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="452ae-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="452ae-104">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="452ae-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="452ae-105">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="452ae-105">What is MessagePack?</span></span>

<span data-ttu-id="452ae-106">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="452ae-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="452ae-107">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="452ae-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="452ae-108">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="452ae-109">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="452ae-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="452ae-110">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="452ae-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="452ae-111">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="452ae-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="452ae-112">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-113">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="452ae-113">JSON is enabled by default.</span></span> <span data-ttu-id="452ae-114">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="452ae-115">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="452ae-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="452ae-116">Nesse delegado, a `SerializerOptions` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="452ae-117">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="452ae-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="452ae-118">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="452ae-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="452ae-119">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="452ae-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="452ae-120">Por exemplo, chamar `.WithSecurity(MessagePackSecurity.UntrustedData)` ao substituir o `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="452ae-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="452ae-121">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="452ae-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-122">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="452ae-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="452ae-123">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="452ae-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="452ae-124">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="452ae-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="452ae-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="452ae-125">.NET client</span></span>

<span data-ttu-id="452ae-126">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="452ae-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="452ae-127">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="452ae-128">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-128">JavaScript client</span></span>

<span data-ttu-id="452ae-129">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="452ae-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="452ae-130">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="452ae-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="452ae-131">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="452ae-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="452ae-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="452ae-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="452ae-133">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="452ae-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="452ae-134">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="452ae-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="452ae-135">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="452ae-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-136">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="452ae-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="452ae-137">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="452ae-138">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="452ae-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="452ae-139">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="452ae-140">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="452ae-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="452ae-141">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="452ae-141">MessagePack quirks</span></span>

<span data-ttu-id="452ae-142">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="452ae-143">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="452ae-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="452ae-144">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="452ae-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="452ae-145">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="452ae-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="452ae-146">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="452ae-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="452ae-147">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="452ae-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="452ae-148">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="452ae-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="452ae-149">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="452ae-150">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="452ae-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="452ae-151">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="452ae-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="452ae-152">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="452ae-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="452ae-153">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack será convertido para o formato UTC se, `DateTime.Kind` `DateTimeKind.Local` caso contrário, ele não tocará o tempo e o passará como está.</span><span class="sxs-lookup"><span data-stu-id="452ae-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="452ae-154">Se você estiver trabalhando com `DateTime` valores, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="452ae-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="452ae-155">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="452ae-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="452ae-156">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="452ae-157">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="452ae-158">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="452ae-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="452ae-159">O valor de `DateTime.MinValue` é `January 1, 0001` , que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="452ae-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="452ae-160">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="452ae-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="452ae-161">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="452ae-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="452ae-162">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="452ae-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="452ae-163">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="452ae-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="452ae-164">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="452ae-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="452ae-165">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="452ae-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="452ae-166">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="452ae-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="452ae-167">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="452ae-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="452ae-168">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="452ae-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="452ae-169">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="452ae-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="452ae-170">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="452ae-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="452ae-171">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="452ae-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="452ae-172">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="452ae-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="452ae-173">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="452ae-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="452ae-174">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="452ae-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="452ae-175">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="452ae-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="452ae-176">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="452ae-176">Related resources</span></span>

* [<span data-ttu-id="452ae-177">Introdução</span><span class="sxs-lookup"><span data-stu-id="452ae-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="452ae-178">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="452ae-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="452ae-179">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="452ae-180">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="452ae-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="452ae-181">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="452ae-181">What is MessagePack?</span></span>

<span data-ttu-id="452ae-182">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="452ae-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="452ae-183">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="452ae-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="452ae-184">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="452ae-185">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="452ae-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="452ae-186">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="452ae-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="452ae-187">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="452ae-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="452ae-188">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-189">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="452ae-189">JSON is enabled by default.</span></span> <span data-ttu-id="452ae-190">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="452ae-191">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="452ae-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="452ae-192">Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="452ae-193">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="452ae-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="452ae-194">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="452ae-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="452ae-195">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="452ae-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="452ae-196">Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="452ae-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="452ae-197">Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="452ae-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="452ae-198">`MessagePack`A instalação das atualizações 1.9. x é usada pela versão SignalR .</span><span class="sxs-lookup"><span data-stu-id="452ae-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="452ae-199">Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData` , um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="452ae-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="452ae-200">Defina `MessagePackSecurity.Active` em `Program.Main` , conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="452ae-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="452ae-201">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="452ae-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-202">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="452ae-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="452ae-203">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="452ae-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="452ae-204">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="452ae-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="452ae-205">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="452ae-205">.NET client</span></span>

<span data-ttu-id="452ae-206">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="452ae-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="452ae-207">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="452ae-208">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-208">JavaScript client</span></span>

<span data-ttu-id="452ae-209">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="452ae-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="452ae-210">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="452ae-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="452ae-211">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="452ae-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="452ae-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="452ae-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="452ae-213">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="452ae-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="452ae-214">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="452ae-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="452ae-215">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="452ae-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-216">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="452ae-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="452ae-217">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="452ae-218">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="452ae-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="452ae-219">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="452ae-220">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="452ae-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="452ae-221">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="452ae-221">MessagePack quirks</span></span>

<span data-ttu-id="452ae-222">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="452ae-223">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="452ae-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="452ae-224">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="452ae-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="452ae-225">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="452ae-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="452ae-226">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="452ae-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="452ae-227">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="452ae-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="452ae-228">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="452ae-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="452ae-229">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="452ae-230">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="452ae-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="452ae-231">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="452ae-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="452ae-232">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="452ae-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="452ae-233">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="452ae-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="452ae-234">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="452ae-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="452ae-235">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="452ae-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="452ae-236">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="452ae-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="452ae-237">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="452ae-238">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="452ae-239">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="452ae-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="452ae-240">O valor de `DateTime.MinValue` é `January 1, 0001` , que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="452ae-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="452ae-241">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="452ae-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="452ae-242">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="452ae-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="452ae-243">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="452ae-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="452ae-244">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="452ae-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="452ae-245">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="452ae-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="452ae-246">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="452ae-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="452ae-247">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="452ae-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="452ae-248">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="452ae-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="452ae-249">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="452ae-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="452ae-250">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="452ae-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="452ae-251">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="452ae-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="452ae-252">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="452ae-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="452ae-253">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="452ae-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="452ae-254">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="452ae-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="452ae-255">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="452ae-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="452ae-256">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="452ae-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="452ae-257">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="452ae-257">Related resources</span></span>

* [<span data-ttu-id="452ae-258">Introdução</span><span class="sxs-lookup"><span data-stu-id="452ae-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="452ae-259">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="452ae-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="452ae-260">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="452ae-261">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="452ae-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="452ae-262">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="452ae-262">What is MessagePack?</span></span>

<span data-ttu-id="452ae-263">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="452ae-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="452ae-264">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="452ae-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="452ae-265">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="452ae-266">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="452ae-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="452ae-267">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="452ae-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="452ae-268">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="452ae-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="452ae-269">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-270">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="452ae-270">JSON is enabled by default.</span></span> <span data-ttu-id="452ae-271">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="452ae-272">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="452ae-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="452ae-273">Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="452ae-274">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="452ae-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="452ae-275">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="452ae-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="452ae-276">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="452ae-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="452ae-277">Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="452ae-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="452ae-278">Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="452ae-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="452ae-279">`MessagePack`A instalação das atualizações 1.9. x é usada pela versão SignalR .</span><span class="sxs-lookup"><span data-stu-id="452ae-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="452ae-280">Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData` , um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="452ae-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="452ae-281">Defina `MessagePackSecurity.Active` em `Program.Main` , conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="452ae-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="452ae-282">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="452ae-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-283">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="452ae-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="452ae-284">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="452ae-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="452ae-285">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="452ae-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="452ae-286">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="452ae-286">.NET client</span></span>

<span data-ttu-id="452ae-287">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="452ae-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="452ae-288">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="452ae-289">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-289">JavaScript client</span></span>

<span data-ttu-id="452ae-290">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="452ae-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="452ae-291">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="452ae-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="452ae-292">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="452ae-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="452ae-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="452ae-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="452ae-294">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="452ae-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="452ae-295">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="452ae-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="452ae-296">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="452ae-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="452ae-297">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="452ae-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="452ae-298">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="452ae-299">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="452ae-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="452ae-300">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="452ae-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="452ae-301">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="452ae-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="452ae-302">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="452ae-302">MessagePack quirks</span></span>

<span data-ttu-id="452ae-303">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="452ae-304">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="452ae-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="452ae-305">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="452ae-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="452ae-306">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="452ae-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="452ae-307">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="452ae-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="452ae-308">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="452ae-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="452ae-309">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="452ae-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="452ae-310">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="452ae-311">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="452ae-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="452ae-312">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="452ae-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="452ae-313">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="452ae-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="452ae-314">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="452ae-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="452ae-315">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="452ae-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="452ae-316">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="452ae-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="452ae-317">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="452ae-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="452ae-318">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="452ae-319">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="452ae-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="452ae-320">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="452ae-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="452ae-321">O valor de `DateTime.MinValue` é `January 1, 0001` que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="452ae-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="452ae-322">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="452ae-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="452ae-323">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="452ae-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="452ae-324">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="452ae-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="452ae-325">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="452ae-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="452ae-326">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="452ae-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="452ae-327">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="452ae-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="452ae-328">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="452ae-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="452ae-329">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="452ae-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="452ae-330">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="452ae-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="452ae-331">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="452ae-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="452ae-332">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="452ae-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="452ae-333">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="452ae-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="452ae-334">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="452ae-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="452ae-335">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="452ae-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="452ae-336">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="452ae-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="452ae-337">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="452ae-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="452ae-338">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="452ae-338">Related resources</span></span>

* [<span data-ttu-id="452ae-339">Introdução</span><span class="sxs-lookup"><span data-stu-id="452ae-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="452ae-340">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="452ae-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="452ae-341">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="452ae-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
