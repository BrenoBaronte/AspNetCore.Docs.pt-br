---
title: Criar mensagens Protobuf para aplicativos .NET
author: jamesnk
description: Saiba como criar mensagens Protobuf para aplicativos .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
ms.openlocfilehash: b70a5ee00405eecfce900b86dc631a54682dce1a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058890"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="212fc-103">Criar mensagens Protobuf para aplicativos .NET</span><span class="sxs-lookup"><span data-stu-id="212fc-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="212fc-104">Por [James Newton – King](https://twitter.com/jamesnk) e [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="212fc-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="212fc-105">gRPC usa [Protobuf](https://developers.google.com/protocol-buffers) como sua IDL (linguagem de definição de interface).</span><span class="sxs-lookup"><span data-stu-id="212fc-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="212fc-106">Protobuf IDL é um formato de linguagem neutra para especificar as mensagens enviadas e recebidas pelos serviços do gRPC.</span><span class="sxs-lookup"><span data-stu-id="212fc-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="212fc-107">As mensagens Protobuf são definidas em `.proto` arquivos.</span><span class="sxs-lookup"><span data-stu-id="212fc-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="212fc-108">Este documento explica como os conceitos do Protobuf são mapeados para o .NET.</span><span class="sxs-lookup"><span data-stu-id="212fc-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="212fc-109">Mensagens de Protobuf</span><span class="sxs-lookup"><span data-stu-id="212fc-109">Protobuf messages</span></span>

<span data-ttu-id="212fc-110">As mensagens são o objeto principal de transferência de dados no Protobuf.</span><span class="sxs-lookup"><span data-stu-id="212fc-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="212fc-111">Elas são conceitualmente semelhantes às classes do .NET.</span><span class="sxs-lookup"><span data-stu-id="212fc-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="212fc-112">A definição de mensagem anterior especifica três campos como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="212fc-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="212fc-113">Como as propriedades em tipos .NET, cada campo tem um nome e um tipo.</span><span class="sxs-lookup"><span data-stu-id="212fc-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="212fc-114">O tipo de campo pode ser um [tipo de valor escalar Protobuf](#scalar-value-types), por exemplo `int32` , ou outra mensagem.</span><span class="sxs-lookup"><span data-stu-id="212fc-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="212fc-115">Além de um nome, cada campo na definição de mensagem tem um número exclusivo.</span><span class="sxs-lookup"><span data-stu-id="212fc-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="212fc-116">Os números de campo são usados para identificar campos quando a mensagem é serializada para Protobuf.</span><span class="sxs-lookup"><span data-stu-id="212fc-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="212fc-117">A serialização de um pequeno número é mais rápida do que a serialização do nome inteiro do campo.</span><span class="sxs-lookup"><span data-stu-id="212fc-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="212fc-118">Como os números de campo identificam um campo, é importante tomar cuidado ao alterá-los.</span><span class="sxs-lookup"><span data-stu-id="212fc-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="212fc-119">Para obter mais informações sobre como alterar mensagens Protobuf, consulte <xref:grpc/versioning> .</span><span class="sxs-lookup"><span data-stu-id="212fc-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="212fc-120">Quando um aplicativo é criado, as ferramentas Protobuf geram tipos .NET de `.proto` arquivos.</span><span class="sxs-lookup"><span data-stu-id="212fc-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="212fc-121">A `Person` mensagem gera uma classe .net:</span><span class="sxs-lookup"><span data-stu-id="212fc-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="212fc-122">Para obter mais informações sobre mensagens Protobuf, consulte o [Guia de idiomas do Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="212fc-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="212fc-123">Tipos de valor escalar</span><span class="sxs-lookup"><span data-stu-id="212fc-123">Scalar Value Types</span></span>

<span data-ttu-id="212fc-124">Protobuf dá suporte a um intervalo de tipos de valor escalar nativos.</span><span class="sxs-lookup"><span data-stu-id="212fc-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="212fc-125">A tabela a seguir lista todos eles com seu tipo C# equivalente:</span><span class="sxs-lookup"><span data-stu-id="212fc-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="212fc-126">Tipo de Protobuf</span><span class="sxs-lookup"><span data-stu-id="212fc-126">Protobuf type</span></span> | <span data-ttu-id="212fc-127">Tipo de C#</span><span class="sxs-lookup"><span data-stu-id="212fc-127">C# type</span></span>      |
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

<span data-ttu-id="212fc-128">Os valores escalares sempre têm um valor padrão e não podem ser definidos como `null` .</span><span class="sxs-lookup"><span data-stu-id="212fc-128">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="212fc-129">Essa restrição inclui `string` e `ByteString` quais são classes C#.</span><span class="sxs-lookup"><span data-stu-id="212fc-129">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="212fc-130">`string` o padrão é um valor de cadeia de caracteres vazia e `ByteString` o padrão é um valor de bytes vazio.</span><span class="sxs-lookup"><span data-stu-id="212fc-130">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="212fc-131">A tentativa de defini-las para `null` gera um erro.</span><span class="sxs-lookup"><span data-stu-id="212fc-131">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="212fc-132">[Tipos de wrapper anuláveis](#nullable-types) podem ser usados para dar suporte a valores nulos.</span><span class="sxs-lookup"><span data-stu-id="212fc-132">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="212fc-133">Datas e horas</span><span class="sxs-lookup"><span data-stu-id="212fc-133">Dates and times</span></span>

<span data-ttu-id="212fc-134">Os tipos escalares nativos não fornecem valores de data e hora, equivalentes a. NET <xref:System.DateTimeOffset> , <xref:System.DateTime> , e <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="212fc-134">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="212fc-135">Esses tipos podem ser especificados usando algumas das extensões de *tipos bem conhecidas* do Protobuf.</span><span class="sxs-lookup"><span data-stu-id="212fc-135">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="212fc-136">Essas extensões fornecem geração de código e suporte a tempo de execução para tipos de campo complexos em plataformas com suporte.</span><span class="sxs-lookup"><span data-stu-id="212fc-136">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="212fc-137">A tabela a seguir mostra os tipos de data e hora:</span><span class="sxs-lookup"><span data-stu-id="212fc-137">The following table shows the date and time types:</span></span>

| <span data-ttu-id="212fc-138">Tipo .NET</span><span class="sxs-lookup"><span data-stu-id="212fc-138">.NET type</span></span>        | <span data-ttu-id="212fc-139">Tipo de Well-Known Protobuf</span><span class="sxs-lookup"><span data-stu-id="212fc-139">Protobuf Well-Known Type</span></span>    |
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

<span data-ttu-id="212fc-140">As propriedades geradas na classe C# não são os tipos de data e hora do .NET.</span><span class="sxs-lookup"><span data-stu-id="212fc-140">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="212fc-141">As propriedades usam as `Timestamp` `Duration` classes e no `Google.Protobuf.WellKnownTypes` namespace.</span><span class="sxs-lookup"><span data-stu-id="212fc-141">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="212fc-142">Essas classes fornecem métodos para converter de e para `DateTimeOffset` , `DateTime` e `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="212fc-142">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

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
> <span data-ttu-id="212fc-143">O `Timestamp` tipo funciona com horas UTC.</span><span class="sxs-lookup"><span data-stu-id="212fc-143">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="212fc-144">`DateTimeOffset` os valores sempre têm um deslocamento de zero e a `DateTime.Kind` propriedade é sempre `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="212fc-144">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="212fc-145">Tipos anuláveis</span><span class="sxs-lookup"><span data-stu-id="212fc-145">Nullable types</span></span>

<span data-ttu-id="212fc-146">A geração de código Protobuf para C# usa os tipos nativos, como `int` para `int32` .</span><span class="sxs-lookup"><span data-stu-id="212fc-146">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="212fc-147">Portanto, os valores são sempre incluídos e não podem ser `null` .</span><span class="sxs-lookup"><span data-stu-id="212fc-147">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="212fc-148">Para valores que exigem explícito `null` , como o uso `int?` de em código c#, os tipos de Well-Known do Protobuf incluem wrappers que são compilados para tipos de C# anuláveis.</span><span class="sxs-lookup"><span data-stu-id="212fc-148">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="212fc-149">Para usá-los, importe `wrappers.proto` para o `.proto` arquivo, como o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="212fc-149">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="212fc-150">`wrappers.proto` os tipos não são expostos em Propriedades geradas.</span><span class="sxs-lookup"><span data-stu-id="212fc-150">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="212fc-151">O Protobuf os mapeia automaticamente para os tipos anuláveis do .NET apropriados em mensagens C#.</span><span class="sxs-lookup"><span data-stu-id="212fc-151">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="212fc-152">Por exemplo, um `google.protobuf.Int32Value` campo gera uma `int?` propriedade.</span><span class="sxs-lookup"><span data-stu-id="212fc-152">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="212fc-153">Propriedades de tipo de referência como `string` e `ByteString` são inalteradas, exceto `null` que podem ser atribuídas a elas sem erro.</span><span class="sxs-lookup"><span data-stu-id="212fc-153">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="212fc-154">A tabela a seguir mostra a lista completa de tipos de wrapper com seu tipo C# equivalente:</span><span class="sxs-lookup"><span data-stu-id="212fc-154">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="212fc-155">Tipo de C#</span><span class="sxs-lookup"><span data-stu-id="212fc-155">C# type</span></span>      | <span data-ttu-id="212fc-156">Wrapper de tipo de Well-Known</span><span class="sxs-lookup"><span data-stu-id="212fc-156">Well-Known Type wrapper</span></span>       |
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

### <a name="bytes"></a><span data-ttu-id="212fc-157">Bytes</span><span class="sxs-lookup"><span data-stu-id="212fc-157">Bytes</span></span>

<span data-ttu-id="212fc-158">Há suporte para cargas binárias em Protobuf com o `bytes` tipo de valor escalar.</span><span class="sxs-lookup"><span data-stu-id="212fc-158">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="212fc-159">Uma propriedade gerada em C# usa `ByteString` como o tipo de propriedade.</span><span class="sxs-lookup"><span data-stu-id="212fc-159">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="212fc-160">Use `ByteString.CopyFrom(byte[] data)` para criar uma nova instância de uma matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="212fc-160">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="212fc-161">`ByteString` os dados são acessados diretamente usando o `ByteString.Span` ou o `ByteString.Memory` .</span><span class="sxs-lookup"><span data-stu-id="212fc-161">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="212fc-162">Ou chame `ByteString.ToByteArray()` para converter uma instância de volta em uma matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="212fc-162">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="212fc-163">Decimais</span><span class="sxs-lookup"><span data-stu-id="212fc-163">Decimals</span></span>

<span data-ttu-id="212fc-164">O Protobuf não dá suporte nativo ao `decimal` tipo .net, apenas `double` e `float` .</span><span class="sxs-lookup"><span data-stu-id="212fc-164">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="212fc-165">Há uma discussão em andamento no projeto Protobuf sobre a possibilidade de adicionar um tipo decimal padrão aos tipos de Well-Known, com suporte de plataforma para linguagens e estruturas que dão suporte a ele.</span><span class="sxs-lookup"><span data-stu-id="212fc-165">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="212fc-166">Nada foi implementado ainda.</span><span class="sxs-lookup"><span data-stu-id="212fc-166">Nothing has been implemented yet.</span></span>

<span data-ttu-id="212fc-167">É possível criar uma definição de mensagem para representar o `decimal` tipo que funciona para a serialização segura entre clientes e servidores .net.</span><span class="sxs-lookup"><span data-stu-id="212fc-167">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="212fc-168">Mas os desenvolvedores de outras plataformas teriam que entender o formato que está sendo usado e implementar sua própria manipulação.</span><span class="sxs-lookup"><span data-stu-id="212fc-168">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="212fc-169">Criando um tipo decimal personalizado para Protobuf</span><span class="sxs-lookup"><span data-stu-id="212fc-169">Creating a custom decimal type for Protobuf</span></span>

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

<span data-ttu-id="212fc-170">O `nanos` campo representa valores de `0.999_999_999` a `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="212fc-170">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="212fc-171">Por exemplo, o `decimal` valor `1.5m` seria representado como `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="212fc-171">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="212fc-172">É por isso que o `nanos` campo neste exemplo usa o `sfixed32` tipo, que codifica com mais eficiência do que `int32` para valores maiores.</span><span class="sxs-lookup"><span data-stu-id="212fc-172">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="212fc-173">Se o `units` campo for negativo, o `nanos` campo também deverá ser negativo.</span><span class="sxs-lookup"><span data-stu-id="212fc-173">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="212fc-174">Há vários outros algoritmos para codificar `decimal` valores como cadeias de caracteres de byte, mas essa mensagem é mais fácil de entender do que qualquer um deles.</span><span class="sxs-lookup"><span data-stu-id="212fc-174">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="212fc-175">Os valores não são afetados por big-endian ou little-endian em plataformas diferentes.</span><span class="sxs-lookup"><span data-stu-id="212fc-175">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="212fc-176">A conversão entre esse tipo e o `decimal` tipo de BCL pode ser implementada em C# da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="212fc-176">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

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

## <a name="collections"></a><span data-ttu-id="212fc-177">Coleções</span><span class="sxs-lookup"><span data-stu-id="212fc-177">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="212fc-178">Listas</span><span class="sxs-lookup"><span data-stu-id="212fc-178">Lists</span></span>

<span data-ttu-id="212fc-179">As listas em Protobuf são especificadas usando a `repeated` palavra-chave prefix em um campo.</span><span class="sxs-lookup"><span data-stu-id="212fc-179">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="212fc-180">O exemplo a seguir mostra como criar uma lista:</span><span class="sxs-lookup"><span data-stu-id="212fc-180">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="212fc-181">No código gerado, `repeated` os campos são representados pelo `Google.Protobuf.Collections.RepeatedField<T>` tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="212fc-181">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="212fc-182">`RepeatedField<T>` implementa <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="212fc-182">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="212fc-183">Portanto, você pode usar consultas LINQ ou convertê-la em uma matriz ou em uma lista.</span><span class="sxs-lookup"><span data-stu-id="212fc-183">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="212fc-184">`RepeatedField<T>` as propriedades não têm um setter público.</span><span class="sxs-lookup"><span data-stu-id="212fc-184">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="212fc-185">Os itens devem ser adicionados à coleção existente.</span><span class="sxs-lookup"><span data-stu-id="212fc-185">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="212fc-186">Dicionários</span><span class="sxs-lookup"><span data-stu-id="212fc-186">Dictionaries</span></span>

<span data-ttu-id="212fc-187">O <xref:System.Collections.Generic.IDictionary%602> tipo .net é representado em Protobuf usando `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="212fc-187">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="212fc-188">No código .NET gerado, `map` os campos são representados pelo `Google.Protobuf.Collections.MapField<TKey, TValue>` tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="212fc-188">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="212fc-189">`MapField<TKey, TValue>` implementa <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="212fc-189">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="212fc-190">Como `repeated` Propriedades, `map` as propriedades não têm um setter público.</span><span class="sxs-lookup"><span data-stu-id="212fc-190">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="212fc-191">Os itens devem ser adicionados à coleção existente.</span><span class="sxs-lookup"><span data-stu-id="212fc-191">Items should be added to the existing collection.</span></span>

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

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="212fc-192">Mensagens não estruturadas e condicionais</span><span class="sxs-lookup"><span data-stu-id="212fc-192">Unstructured and conditional messages</span></span>

<span data-ttu-id="212fc-193">Protobuf é um formato de mensagens do primeiro contrato.</span><span class="sxs-lookup"><span data-stu-id="212fc-193">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="212fc-194">As mensagens de um aplicativo, incluindo seus campos e tipos, devem ser especificadas em `.proto` arquivos quando o aplicativo é compilado.</span><span class="sxs-lookup"><span data-stu-id="212fc-194">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="212fc-195">O primeiro design de contrato do Protobuf é ótimo na imposição do conteúdo da mensagem, mas pode limitar os cenários em que um contrato estrito não é necessário:</span><span class="sxs-lookup"><span data-stu-id="212fc-195">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="212fc-196">Mensagens com cargas desconhecidas.</span><span class="sxs-lookup"><span data-stu-id="212fc-196">Messages with unknown payloads.</span></span> <span data-ttu-id="212fc-197">Por exemplo, uma mensagem com um campo que pode conter qualquer mensagem.</span><span class="sxs-lookup"><span data-stu-id="212fc-197">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="212fc-198">Mensagens condicionais.</span><span class="sxs-lookup"><span data-stu-id="212fc-198">Conditional messages.</span></span> <span data-ttu-id="212fc-199">Por exemplo, uma mensagem retornada de um serviço gRPC pode ser um resultado de êxito ou um resultado de erro.</span><span class="sxs-lookup"><span data-stu-id="212fc-199">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="212fc-200">Valores dinâmicos.</span><span class="sxs-lookup"><span data-stu-id="212fc-200">Dynamic values.</span></span> <span data-ttu-id="212fc-201">Por exemplo, uma mensagem com um campo que contém uma coleção não estruturada de valores, semelhante ao JSON.</span><span class="sxs-lookup"><span data-stu-id="212fc-201">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="212fc-202">O Protobuf oferece recursos e tipos de linguagem para dar suporte a esses cenários.</span><span class="sxs-lookup"><span data-stu-id="212fc-202">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="212fc-203">Qualquer</span><span class="sxs-lookup"><span data-stu-id="212fc-203">Any</span></span>

<span data-ttu-id="212fc-204">O `Any` tipo permite que você use mensagens como tipos inseridos sem ter sua `.proto` definição.</span><span class="sxs-lookup"><span data-stu-id="212fc-204">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="212fc-205">Para usar o `Any` tipo, importe `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="212fc-205">To use the `Any` type, import `any.proto`.</span></span>

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

### <a name="oneof"></a><span data-ttu-id="212fc-206">Oneof</span><span class="sxs-lookup"><span data-stu-id="212fc-206">Oneof</span></span>

<span data-ttu-id="212fc-207">`oneof` os campos são um recurso de idioma.</span><span class="sxs-lookup"><span data-stu-id="212fc-207">`oneof` fields are a language feature.</span></span> <span data-ttu-id="212fc-208">O compilador manipula a `oneof` palavra-chave quando gera a classe da mensagem.</span><span class="sxs-lookup"><span data-stu-id="212fc-208">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="212fc-209">Usando `oneof` para especificar uma mensagem de resposta que poderia retornar `Person` ou `Error` pode ter esta aparência:</span><span class="sxs-lookup"><span data-stu-id="212fc-209">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

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

<span data-ttu-id="212fc-210">Os campos dentro do `oneof` conjunto devem ter números de campo exclusivos na declaração de mensagem geral.</span><span class="sxs-lookup"><span data-stu-id="212fc-210">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="212fc-211">Ao usar `oneof` o, o código C# gerado inclui uma enumeração que especifica quais dos campos foram definidos.</span><span class="sxs-lookup"><span data-stu-id="212fc-211">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="212fc-212">Você pode testar a enumeração para localizar qual campo está definido.</span><span class="sxs-lookup"><span data-stu-id="212fc-212">You can test the enum to find which field is set.</span></span> <span data-ttu-id="212fc-213">Campos que não são definidos como Return `null` ou o valor padrão, em vez de lançar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="212fc-213">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

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

### <a name="value"></a><span data-ttu-id="212fc-214">Valor</span><span class="sxs-lookup"><span data-stu-id="212fc-214">Value</span></span>

<span data-ttu-id="212fc-215">O `Value` tipo representa um valor digitado dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="212fc-215">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="212fc-216">Pode ser `null` , um número, uma cadeia de caracteres, um booliano, um dicionário de valores ( `Struct` ) ou uma lista de valores ( `ValueList` ).</span><span class="sxs-lookup"><span data-stu-id="212fc-216">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="212fc-217">`Value` é um tipo de Well-Known Protobuf que usa o recurso discutido anteriormente `oneof` .</span><span class="sxs-lookup"><span data-stu-id="212fc-217">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="212fc-218">Para usar o `Value` tipo, importe `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="212fc-218">To use the `Value` type, import `struct.proto`.</span></span>

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

<span data-ttu-id="212fc-219">Usar `Value` diretamente pode ser detalhado.</span><span class="sxs-lookup"><span data-stu-id="212fc-219">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="212fc-220">Uma maneira alternativa de usar o `Value` é com o suporte interno do Protobuf para mapear mensagens para JSON.</span><span class="sxs-lookup"><span data-stu-id="212fc-220">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="212fc-221">`JsonFormatter` `JsonWriter` Os tipos e Protobuf podem ser usados com qualquer mensagem Protobuf.</span><span class="sxs-lookup"><span data-stu-id="212fc-221">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="212fc-222">`Value` é particularmente adequado para ser convertido de e para JSON.</span><span class="sxs-lookup"><span data-stu-id="212fc-222">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="212fc-223">Esse é o equivalente JSON do código anterior:</span><span class="sxs-lookup"><span data-stu-id="212fc-223">This is the JSON equivalent of the previous code:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="212fc-224">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="212fc-224">Additional resources</span></span>

* [<span data-ttu-id="212fc-225">Guia de linguagem Protobuf</span><span class="sxs-lookup"><span data-stu-id="212fc-225">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
