---
title: Criar mensagens Protobuf para aplicativos .NET
author: jamesnk
description: Saiba como criar mensagens Protobuf para aplicativos .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/12/2021
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
uid: grpc/protobuf
ms.openlocfilehash: adb048c9b20e172ef8a7bc13f9b56928b1d4bf77
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280288"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="92670-103">Criar mensagens Protobuf para aplicativos .NET</span><span class="sxs-lookup"><span data-stu-id="92670-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="92670-104">Por [James Newton – King](https://twitter.com/jamesnk) e [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="92670-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="92670-105">gRPC usa [Protobuf](https://developers.google.com/protocol-buffers) como sua IDL (linguagem de definição de interface).</span><span class="sxs-lookup"><span data-stu-id="92670-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="92670-106">Protobuf IDL é um formato de linguagem neutra para especificar as mensagens enviadas e recebidas pelos serviços do gRPC.</span><span class="sxs-lookup"><span data-stu-id="92670-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="92670-107">As mensagens Protobuf são definidas em `.proto` arquivos.</span><span class="sxs-lookup"><span data-stu-id="92670-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="92670-108">Este documento explica como os conceitos do Protobuf são mapeados para o .NET.</span><span class="sxs-lookup"><span data-stu-id="92670-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="92670-109">Mensagens de Protobuf</span><span class="sxs-lookup"><span data-stu-id="92670-109">Protobuf messages</span></span>

<span data-ttu-id="92670-110">As mensagens são o objeto principal de transferência de dados no Protobuf.</span><span class="sxs-lookup"><span data-stu-id="92670-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="92670-111">Elas são conceitualmente semelhantes às classes do .NET.</span><span class="sxs-lookup"><span data-stu-id="92670-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="92670-112">A definição de mensagem anterior especifica três campos como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="92670-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="92670-113">Como as propriedades em tipos .NET, cada campo tem um nome e um tipo.</span><span class="sxs-lookup"><span data-stu-id="92670-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="92670-114">O tipo de campo pode ser um [tipo de valor escalar Protobuf](#scalar-value-types), por exemplo `int32` , ou outra mensagem.</span><span class="sxs-lookup"><span data-stu-id="92670-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="92670-115">O [Guia de estilo Protobuf](https://developers.google.com/protocol-buffers/docs/style) recomenda o uso `underscore_separated_names` de nomes de campo.</span><span class="sxs-lookup"><span data-stu-id="92670-115">The [Protobuf style guide](https://developers.google.com/protocol-buffers/docs/style) recommends using `underscore_separated_names` for field names.</span></span> <span data-ttu-id="92670-116">Novas mensagens Protobuf criadas para aplicativos .NET devem seguir as diretrizes de estilo Protobuf.</span><span class="sxs-lookup"><span data-stu-id="92670-116">New Protobuf messages created for .NET apps should follow the Protobuf style guidelines.</span></span> <span data-ttu-id="92670-117">As ferramentas do .NET geram automaticamente tipos .NET que usam os padrões de nomenclatura do .NET.</span><span class="sxs-lookup"><span data-stu-id="92670-117">.NET tooling automatically generates .NET types that use .NET naming standards.</span></span> <span data-ttu-id="92670-118">Por exemplo, um `first_name` campo Protobuf gera uma `FirstName` propriedade .net.</span><span class="sxs-lookup"><span data-stu-id="92670-118">For example, a `first_name` Protobuf field generates a `FirstName` .NET property.</span></span>

<span data-ttu-id="92670-119">Além de um nome, cada campo na definição de mensagem tem um número exclusivo.</span><span class="sxs-lookup"><span data-stu-id="92670-119">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="92670-120">Os números de campo são usados para identificar campos quando a mensagem é serializada para Protobuf.</span><span class="sxs-lookup"><span data-stu-id="92670-120">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="92670-121">A serialização de um pequeno número é mais rápida do que a serialização do nome inteiro do campo.</span><span class="sxs-lookup"><span data-stu-id="92670-121">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="92670-122">Como os números de campo identificam um campo, é importante tomar cuidado ao alterá-los.</span><span class="sxs-lookup"><span data-stu-id="92670-122">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="92670-123">Para obter mais informações sobre como alterar mensagens Protobuf, consulte <xref:grpc/versioning> .</span><span class="sxs-lookup"><span data-stu-id="92670-123">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="92670-124">Quando um aplicativo é criado, as ferramentas Protobuf geram tipos .NET de `.proto` arquivos.</span><span class="sxs-lookup"><span data-stu-id="92670-124">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="92670-125">A `Person` mensagem gera uma classe .net:</span><span class="sxs-lookup"><span data-stu-id="92670-125">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="92670-126">Para obter mais informações sobre mensagens Protobuf, consulte o [Guia de idiomas do Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="92670-126">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="92670-127">Tipos de valor escalar</span><span class="sxs-lookup"><span data-stu-id="92670-127">Scalar Value Types</span></span>

<span data-ttu-id="92670-128">Protobuf dá suporte a um intervalo de tipos de valor escalar nativos.</span><span class="sxs-lookup"><span data-stu-id="92670-128">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="92670-129">A tabela a seguir lista todos eles com seu tipo C# equivalente:</span><span class="sxs-lookup"><span data-stu-id="92670-129">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="92670-130">Tipo de Protobuf</span><span class="sxs-lookup"><span data-stu-id="92670-130">Protobuf type</span></span> | <span data-ttu-id="92670-131">Tipo de C#</span><span class="sxs-lookup"><span data-stu-id="92670-131">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

<span data-ttu-id="92670-132">Os valores escalares sempre têm um valor padrão e não podem ser definidos como `null` .</span><span class="sxs-lookup"><span data-stu-id="92670-132">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="92670-133">Essa restrição inclui `string` e `ByteString` quais são classes C#.</span><span class="sxs-lookup"><span data-stu-id="92670-133">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="92670-134">`string` o padrão é um valor de cadeia de caracteres vazia e `ByteString` o padrão é um valor de bytes vazio.</span><span class="sxs-lookup"><span data-stu-id="92670-134">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="92670-135">A tentativa de defini-las para `null` gera um erro.</span><span class="sxs-lookup"><span data-stu-id="92670-135">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="92670-136">[Tipos de wrapper anuláveis](#nullable-types) podem ser usados para dar suporte a valores nulos.</span><span class="sxs-lookup"><span data-stu-id="92670-136">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="92670-137">Datas e horas</span><span class="sxs-lookup"><span data-stu-id="92670-137">Dates and times</span></span>

<span data-ttu-id="92670-138">Os tipos escalares nativos não fornecem valores de data e hora, equivalentes a. NET <xref:System.DateTimeOffset> , <xref:System.DateTime> , e <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="92670-138">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="92670-139">Esses tipos podem ser especificados usando algumas das extensões de *tipos bem conhecidas* do Protobuf.</span><span class="sxs-lookup"><span data-stu-id="92670-139">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="92670-140">Essas extensões fornecem geração de código e suporte a tempo de execução para tipos de campo complexos em plataformas com suporte.</span><span class="sxs-lookup"><span data-stu-id="92670-140">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="92670-141">A tabela a seguir mostra os tipos de data e hora:</span><span class="sxs-lookup"><span data-stu-id="92670-141">The following table shows the date and time types:</span></span>

| <span data-ttu-id="92670-142">Tipo .NET</span><span class="sxs-lookup"><span data-stu-id="92670-142">.NET type</span></span>        | <span data-ttu-id="92670-143">Tipo de Well-Known Protobuf</span><span class="sxs-lookup"><span data-stu-id="92670-143">Protobuf Well-Known Type</span></span>    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="92670-144">As propriedades geradas na classe C# não são os tipos de data e hora do .NET.</span><span class="sxs-lookup"><span data-stu-id="92670-144">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="92670-145">As propriedades usam as `Timestamp` `Duration` classes e no `Google.Protobuf.WellKnownTypes` namespace.</span><span class="sxs-lookup"><span data-stu-id="92670-145">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="92670-146">Essas classes fornecem métodos para converter de e para `DateTimeOffset` , `DateTime` e `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="92670-146">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="92670-147">O `Timestamp` tipo funciona com horas UTC.</span><span class="sxs-lookup"><span data-stu-id="92670-147">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="92670-148">`DateTimeOffset` os valores sempre têm um deslocamento de zero e a `DateTime.Kind` propriedade é sempre `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="92670-148">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="92670-149">Tipos anuláveis</span><span class="sxs-lookup"><span data-stu-id="92670-149">Nullable types</span></span>

<span data-ttu-id="92670-150">A geração de código Protobuf para C# usa os tipos nativos, como `int` para `int32` .</span><span class="sxs-lookup"><span data-stu-id="92670-150">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="92670-151">Portanto, os valores são sempre incluídos e não podem ser `null` .</span><span class="sxs-lookup"><span data-stu-id="92670-151">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="92670-152">Para valores que exigem explícito `null` , como o uso `int?` de em código c#, os tipos de Well-Known do Protobuf incluem wrappers que são compilados para tipos de C# anuláveis.</span><span class="sxs-lookup"><span data-stu-id="92670-152">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="92670-153">Para usá-los, importe `wrappers.proto` para o `.proto` arquivo, como o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="92670-153">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="92670-154">`wrappers.proto` os tipos não são expostos em Propriedades geradas.</span><span class="sxs-lookup"><span data-stu-id="92670-154">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="92670-155">O Protobuf os mapeia automaticamente para os tipos anuláveis do .NET apropriados em mensagens C#.</span><span class="sxs-lookup"><span data-stu-id="92670-155">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="92670-156">Por exemplo, um `google.protobuf.Int32Value` campo gera uma `int?` propriedade.</span><span class="sxs-lookup"><span data-stu-id="92670-156">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="92670-157">Propriedades de tipo de referência como `string` e `ByteString` são inalteradas, exceto `null` que podem ser atribuídas a elas sem erro.</span><span class="sxs-lookup"><span data-stu-id="92670-157">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="92670-158">A tabela a seguir mostra a lista completa de tipos de wrapper com seu tipo C# equivalente:</span><span class="sxs-lookup"><span data-stu-id="92670-158">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="92670-159">Tipo de C#</span><span class="sxs-lookup"><span data-stu-id="92670-159">C# type</span></span>      | <span data-ttu-id="92670-160">Wrapper de tipo de Well-Known</span><span class="sxs-lookup"><span data-stu-id="92670-160">Well-Known Type wrapper</span></span>       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a><span data-ttu-id="92670-161">Bytes</span><span class="sxs-lookup"><span data-stu-id="92670-161">Bytes</span></span>

<span data-ttu-id="92670-162">Há suporte para cargas binárias em Protobuf com o `bytes` tipo de valor escalar.</span><span class="sxs-lookup"><span data-stu-id="92670-162">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="92670-163">Uma propriedade gerada em C# usa `ByteString` como o tipo de propriedade.</span><span class="sxs-lookup"><span data-stu-id="92670-163">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="92670-164">Use `ByteString.CopyFrom(byte[] data)` para criar uma nova instância de uma matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="92670-164">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="92670-165">`ByteString` os dados são acessados diretamente usando o `ByteString.Span` ou o `ByteString.Memory` .</span><span class="sxs-lookup"><span data-stu-id="92670-165">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="92670-166">Ou chame `ByteString.ToByteArray()` para converter uma instância de volta em uma matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="92670-166">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="92670-167">Decimais</span><span class="sxs-lookup"><span data-stu-id="92670-167">Decimals</span></span>

<span data-ttu-id="92670-168">O Protobuf não dá suporte nativo ao `decimal` tipo .net, apenas `double` e `float` .</span><span class="sxs-lookup"><span data-stu-id="92670-168">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="92670-169">Há uma discussão em andamento no projeto Protobuf sobre a possibilidade de adicionar um tipo decimal padrão aos tipos de Well-Known, com suporte de plataforma para linguagens e estruturas que dão suporte a ele.</span><span class="sxs-lookup"><span data-stu-id="92670-169">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="92670-170">Nada foi implementado ainda.</span><span class="sxs-lookup"><span data-stu-id="92670-170">Nothing has been implemented yet.</span></span>

<span data-ttu-id="92670-171">É possível criar uma definição de mensagem para representar o `decimal` tipo que funciona para a serialização segura entre clientes e servidores .net.</span><span class="sxs-lookup"><span data-stu-id="92670-171">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="92670-172">Mas os desenvolvedores de outras plataformas teriam que entender o formato que está sendo usado e implementar sua própria manipulação.</span><span class="sxs-lookup"><span data-stu-id="92670-172">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="92670-173">Criando um tipo decimal personalizado para Protobuf</span><span class="sxs-lookup"><span data-stu-id="92670-173">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="92670-174">O `nanos` campo representa valores de `0.999_999_999` a `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="92670-174">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="92670-175">Por exemplo, o `decimal` valor `1.5m` seria representado como `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="92670-175">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="92670-176">É por isso que o `nanos` campo neste exemplo usa o `sfixed32` tipo, que codifica com mais eficiência do que `int32` para valores maiores.</span><span class="sxs-lookup"><span data-stu-id="92670-176">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="92670-177">Se o `units` campo for negativo, o `nanos` campo também deverá ser negativo.</span><span class="sxs-lookup"><span data-stu-id="92670-177">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="92670-178">Há vários outros algoritmos para codificar `decimal` valores como cadeias de caracteres de byte, mas essa mensagem é mais fácil de entender do que qualquer um deles.</span><span class="sxs-lookup"><span data-stu-id="92670-178">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="92670-179">Os valores não são afetados por big-endian ou little-endian em plataformas diferentes.</span><span class="sxs-lookup"><span data-stu-id="92670-179">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="92670-180">A conversão entre esse tipo e o `decimal` tipo de BCL pode ser implementada em C# da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="92670-180">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="92670-181">Coleções</span><span class="sxs-lookup"><span data-stu-id="92670-181">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="92670-182">Listas</span><span class="sxs-lookup"><span data-stu-id="92670-182">Lists</span></span>

<span data-ttu-id="92670-183">As listas em Protobuf são especificadas usando a `repeated` palavra-chave prefix em um campo.</span><span class="sxs-lookup"><span data-stu-id="92670-183">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="92670-184">O exemplo a seguir mostra como criar uma lista:</span><span class="sxs-lookup"><span data-stu-id="92670-184">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="92670-185">No código gerado, `repeated` os campos são representados pelo `Google.Protobuf.Collections.RepeatedField<T>` tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="92670-185">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="92670-186">`RepeatedField<T>` implementa <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="92670-186">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="92670-187">Portanto, você pode usar consultas LINQ ou convertê-la em uma matriz ou em uma lista.</span><span class="sxs-lookup"><span data-stu-id="92670-187">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="92670-188">`RepeatedField<T>` as propriedades não têm um setter público.</span><span class="sxs-lookup"><span data-stu-id="92670-188">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="92670-189">Os itens devem ser adicionados à coleção existente.</span><span class="sxs-lookup"><span data-stu-id="92670-189">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="92670-190">Dicionários</span><span class="sxs-lookup"><span data-stu-id="92670-190">Dictionaries</span></span>

<span data-ttu-id="92670-191">O <xref:System.Collections.Generic.IDictionary%602> tipo .net é representado em Protobuf usando `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="92670-191">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="92670-192">No código .NET gerado, `map` os campos são representados pelo `Google.Protobuf.Collections.MapField<TKey, TValue>` tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="92670-192">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="92670-193">`MapField<TKey, TValue>` implementa <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="92670-193">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="92670-194">Como `repeated` Propriedades, `map` as propriedades não têm um setter público.</span><span class="sxs-lookup"><span data-stu-id="92670-194">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="92670-195">Os itens devem ser adicionados à coleção existente.</span><span class="sxs-lookup"><span data-stu-id="92670-195">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="92670-196">Mensagens não estruturadas e condicionais</span><span class="sxs-lookup"><span data-stu-id="92670-196">Unstructured and conditional messages</span></span>

<span data-ttu-id="92670-197">Protobuf é um formato de mensagens do primeiro contrato.</span><span class="sxs-lookup"><span data-stu-id="92670-197">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="92670-198">As mensagens de um aplicativo, incluindo seus campos e tipos, devem ser especificadas em `.proto` arquivos quando o aplicativo é compilado.</span><span class="sxs-lookup"><span data-stu-id="92670-198">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="92670-199">O primeiro design de contrato do Protobuf é ótimo na imposição do conteúdo da mensagem, mas pode limitar os cenários em que um contrato estrito não é necessário:</span><span class="sxs-lookup"><span data-stu-id="92670-199">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="92670-200">Mensagens com cargas desconhecidas.</span><span class="sxs-lookup"><span data-stu-id="92670-200">Messages with unknown payloads.</span></span> <span data-ttu-id="92670-201">Por exemplo, uma mensagem com um campo que pode conter qualquer mensagem.</span><span class="sxs-lookup"><span data-stu-id="92670-201">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="92670-202">Mensagens condicionais.</span><span class="sxs-lookup"><span data-stu-id="92670-202">Conditional messages.</span></span> <span data-ttu-id="92670-203">Por exemplo, uma mensagem retornada de um serviço gRPC pode ser um resultado de êxito ou um resultado de erro.</span><span class="sxs-lookup"><span data-stu-id="92670-203">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="92670-204">Valores dinâmicos.</span><span class="sxs-lookup"><span data-stu-id="92670-204">Dynamic values.</span></span> <span data-ttu-id="92670-205">Por exemplo, uma mensagem com um campo que contém uma coleção não estruturada de valores, semelhante ao JSON.</span><span class="sxs-lookup"><span data-stu-id="92670-205">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="92670-206">O Protobuf oferece recursos e tipos de linguagem para dar suporte a esses cenários.</span><span class="sxs-lookup"><span data-stu-id="92670-206">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="92670-207">Qualquer</span><span class="sxs-lookup"><span data-stu-id="92670-207">Any</span></span>

<span data-ttu-id="92670-208">O `Any` tipo permite que você use mensagens como tipos inseridos sem ter sua `.proto` definição.</span><span class="sxs-lookup"><span data-stu-id="92670-208">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="92670-209">Para usar o `Any` tipo, importe `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="92670-209">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="92670-210">Oneof</span><span class="sxs-lookup"><span data-stu-id="92670-210">Oneof</span></span>

<span data-ttu-id="92670-211">`oneof` os campos são um recurso de idioma.</span><span class="sxs-lookup"><span data-stu-id="92670-211">`oneof` fields are a language feature.</span></span> <span data-ttu-id="92670-212">O compilador manipula a `oneof` palavra-chave quando gera a classe da mensagem.</span><span class="sxs-lookup"><span data-stu-id="92670-212">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="92670-213">Usando `oneof` para especificar uma mensagem de resposta que poderia retornar `Person` ou `Error` pode ter esta aparência:</span><span class="sxs-lookup"><span data-stu-id="92670-213">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="92670-214">Os campos dentro do `oneof` conjunto devem ter números de campo exclusivos na declaração de mensagem geral.</span><span class="sxs-lookup"><span data-stu-id="92670-214">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="92670-215">Ao usar `oneof` o, o código C# gerado inclui uma enumeração que especifica quais dos campos foram definidos.</span><span class="sxs-lookup"><span data-stu-id="92670-215">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="92670-216">Você pode testar a enumeração para localizar qual campo está definido.</span><span class="sxs-lookup"><span data-stu-id="92670-216">You can test the enum to find which field is set.</span></span> <span data-ttu-id="92670-217">Campos que não são definidos como Return `null` ou o valor padrão, em vez de lançar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="92670-217">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="92670-218">Valor</span><span class="sxs-lookup"><span data-stu-id="92670-218">Value</span></span>

<span data-ttu-id="92670-219">O `Value` tipo representa um valor digitado dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="92670-219">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="92670-220">Pode ser `null` , um número, uma cadeia de caracteres, um booliano, um dicionário de valores ( `Struct` ) ou uma lista de valores ( `ValueList` ).</span><span class="sxs-lookup"><span data-stu-id="92670-220">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="92670-221">`Value` é um tipo de Well-Known Protobuf que usa o recurso discutido anteriormente `oneof` .</span><span class="sxs-lookup"><span data-stu-id="92670-221">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="92670-222">Para usar o `Value` tipo, importe `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="92670-222">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="92670-223">Usar `Value` diretamente pode ser detalhado.</span><span class="sxs-lookup"><span data-stu-id="92670-223">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="92670-224">Uma maneira alternativa de usar o `Value` é com o suporte interno do Protobuf para mapear mensagens para JSON.</span><span class="sxs-lookup"><span data-stu-id="92670-224">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="92670-225">`JsonFormatter` `JsonWriter` Os tipos e Protobuf podem ser usados com qualquer mensagem Protobuf.</span><span class="sxs-lookup"><span data-stu-id="92670-225">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="92670-226">`Value` é particularmente adequado para ser convertido de e para JSON.</span><span class="sxs-lookup"><span data-stu-id="92670-226">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="92670-227">Esse é o equivalente JSON do código anterior:</span><span class="sxs-lookup"><span data-stu-id="92670-227">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="92670-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="92670-228">Additional resources</span></span>

* [<span data-ttu-id="92670-229">Guia de linguagem Protobuf</span><span class="sxs-lookup"><span data-stu-id="92670-229">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
