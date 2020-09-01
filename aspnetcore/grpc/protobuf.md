---
title: Создание сообщений protobuf для приложений .NET
author: jamesnk
description: Узнайте, как создавать сообщения protobuf для приложений .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: b8149b79c1e7b204e52cc8595d1193b623bb0008
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945526"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="f9194-103">Создание сообщений protobuf для приложений .NET</span><span class="sxs-lookup"><span data-stu-id="f9194-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="f9194-104">Авторы: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King) и [Марк Рендл](https://twitter.com/markrendle) (Mark Rendle)</span><span class="sxs-lookup"><span data-stu-id="f9194-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="f9194-105">gRPC использует [protobuf](https://developers.google.com/protocol-buffers) в качестве языка определения интерфейса (IDL).</span><span class="sxs-lookup"><span data-stu-id="f9194-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="f9194-106">Protobuf IDL — это не зависящий от языка формат для указания сообщений, отправляемых и получаемых службами gRPC.</span><span class="sxs-lookup"><span data-stu-id="f9194-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="f9194-107">Сообщения protobuf определяются в файлах *.proto*.</span><span class="sxs-lookup"><span data-stu-id="f9194-107">Protobuf messages are defined in *.proto* files.</span></span> <span data-ttu-id="f9194-108">В этом документе объясняется, как концепции protobuf соотносятся с .NET.</span><span class="sxs-lookup"><span data-stu-id="f9194-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="f9194-109">Сообщения Protobuf</span><span class="sxs-lookup"><span data-stu-id="f9194-109">Protobuf messages</span></span>

<span data-ttu-id="f9194-110">Сообщения — это основной объект для обмена данными в protobuf.</span><span class="sxs-lookup"><span data-stu-id="f9194-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="f9194-111">Они концептуально схожи с классами .NET.</span><span class="sxs-lookup"><span data-stu-id="f9194-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="f9194-112">В предыдущем определении сообщения указывается три поля в качестве пар "имя — значение".</span><span class="sxs-lookup"><span data-stu-id="f9194-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="f9194-113">Как и свойства типов .NET, каждое поле имеет имя и тип.</span><span class="sxs-lookup"><span data-stu-id="f9194-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="f9194-114">Типом поля может быть [скалярный тип значения protobuf](#scalar-value-types), например `int32`, или другое сообщение.</span><span class="sxs-lookup"><span data-stu-id="f9194-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="f9194-115">Помимо имени, каждое поле в определении сообщения имеет уникальный номер.</span><span class="sxs-lookup"><span data-stu-id="f9194-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="f9194-116">Номера полей используются для задания полей при сериализации сообщения в protobuf.</span><span class="sxs-lookup"><span data-stu-id="f9194-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="f9194-117">Сериализация небольшого числа выполняется быстрее, чем сериализация всего имени поля.</span><span class="sxs-lookup"><span data-stu-id="f9194-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="f9194-118">Поскольку номера полей указывают на поле, важно соблюдать осторожность при их изменении.</span><span class="sxs-lookup"><span data-stu-id="f9194-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="f9194-119">Дополнительные сведения об изменении сообщений protobuf см. в разделе <xref:grpc/versioning>.</span><span class="sxs-lookup"><span data-stu-id="f9194-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="f9194-120">При построении приложения средства protobuf создают типы .NET из файлов *.proto*.</span><span class="sxs-lookup"><span data-stu-id="f9194-120">When an app is built the Protobuf tooling generates .NET types from *.proto* files.</span></span> <span data-ttu-id="f9194-121">Сообщение `Person` создает класс .NET:</span><span class="sxs-lookup"><span data-stu-id="f9194-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="f9194-122">Дополнительные сведения о сообщениях protobuf см. в разделе [Руководство по языку protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="f9194-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="f9194-123">Скалярные типы значений</span><span class="sxs-lookup"><span data-stu-id="f9194-123">Scalar Value Types</span></span>

<span data-ttu-id="f9194-124">Protobuf поддерживает ряд собственных скалярных типов значений.</span><span class="sxs-lookup"><span data-stu-id="f9194-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="f9194-125">В следующей таблице перечислены все типы с эквивалентными им типами в C#.</span><span class="sxs-lookup"><span data-stu-id="f9194-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="f9194-126">Тип protobuf</span><span class="sxs-lookup"><span data-stu-id="f9194-126">Protobuf type</span></span> | <span data-ttu-id="f9194-127">Тип C#</span><span class="sxs-lookup"><span data-stu-id="f9194-127">C# type</span></span>      |
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

### <a name="dates-and-times"></a><span data-ttu-id="f9194-128">Даты и время</span><span class="sxs-lookup"><span data-stu-id="f9194-128">Dates and times</span></span>

<span data-ttu-id="f9194-129">Собственные скалярные типы не предоставляют значения даты и времени, что эквивалентно <xref:System.DateTimeOffset>, <xref:System.DateTime> и <xref:System.TimeSpan> в .NET.</span><span class="sxs-lookup"><span data-stu-id="f9194-129">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="f9194-130">Эти типы могут быть заданы с помощью некоторых из расширений protobuf хорошо известных типов.</span><span class="sxs-lookup"><span data-stu-id="f9194-130">These types can be specified by using some of Protobuf's "Well Known Types" extensions.</span></span> <span data-ttu-id="f9194-131">Эти расширения обеспечивают поддержку создания кода и среды выполнения для сложных типов полей в поддерживаемых платформах.</span><span class="sxs-lookup"><span data-stu-id="f9194-131">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="f9194-132">В следующей таблице показаны типы даты и времени.</span><span class="sxs-lookup"><span data-stu-id="f9194-132">The following table shows the date and time types:</span></span>

| <span data-ttu-id="f9194-133">Тип .NET</span><span class="sxs-lookup"><span data-stu-id="f9194-133">.NET type</span></span> | <span data-ttu-id="f9194-134">Хорошо известный тип protobuf</span><span class="sxs-lookup"><span data-stu-id="f9194-134">Protobuf well-known type</span></span> |
| ------- | ------------------------ |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime` | `google.protobuf.Timestamp` |
| `TimeSpan` | `google.protobuf.Duration` |

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

<span data-ttu-id="f9194-135">Созданные свойства в классе C# не являются типами даты и времени .NET.</span><span class="sxs-lookup"><span data-stu-id="f9194-135">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="f9194-136">Свойства используют классы `Timestamp` и `Duration` в пространстве имен `Google.Protobuf.WellKnownTypes`.</span><span class="sxs-lookup"><span data-stu-id="f9194-136">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="f9194-137">Эти классы предоставляют методы для преобразования в `DateTimeOffset`, `DateTime` и `TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="f9194-137">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

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
> <span data-ttu-id="f9194-138">Тип `Timestamp` работает с временем в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="f9194-138">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="f9194-139">Значения `DateTimeOffset` всегда имеют нулевое смещение, а свойство `DateTime.Kind` всегда имеет значение `DateTimeKind.Utc`.</span><span class="sxs-lookup"><span data-stu-id="f9194-139">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="f9194-140">Типы, допускающие значение NULL</span><span class="sxs-lookup"><span data-stu-id="f9194-140">Nullable types</span></span>

<span data-ttu-id="f9194-141">При создании кода protobuf для C# используются собственные типы, например `int` для `int32`.</span><span class="sxs-lookup"><span data-stu-id="f9194-141">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="f9194-142">Поэтому значения всегда включаются и не могут быть `null`.</span><span class="sxs-lookup"><span data-stu-id="f9194-142">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="f9194-143">Для значений, требующих явного `null`, таких как использование `int?` в коде C#, хорошо известные типы protobuf включают оболочки, компилируемые в типы C#, допускающие значение NULL.</span><span class="sxs-lookup"><span data-stu-id="f9194-143">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's "Well Known Types" include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="f9194-144">Чтобы использовать их, импортируйте `wrappers.proto` в файл `.proto`, как в следующем коде.</span><span class="sxs-lookup"><span data-stu-id="f9194-144">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="f9194-145">Protobuf использует типы .NET, допускающие значение NULL, например `int?`, для созданного свойства сообщения.</span><span class="sxs-lookup"><span data-stu-id="f9194-145">Protobuf uses .NET nullable types, for example, `int?`, for the generated message property.</span></span>

<span data-ttu-id="f9194-146">В следующей таблице приведен полный список типов оболочек с эквивалентным им типом C#.</span><span class="sxs-lookup"><span data-stu-id="f9194-146">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="f9194-147">Тип C#</span><span class="sxs-lookup"><span data-stu-id="f9194-147">C# type</span></span>   | <span data-ttu-id="f9194-148">Оболочка хорошо известного типа</span><span class="sxs-lookup"><span data-stu-id="f9194-148">Well Known Type wrapper</span></span>       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a><span data-ttu-id="f9194-149">Десятичные знаки</span><span class="sxs-lookup"><span data-stu-id="f9194-149">Decimals</span></span>

<span data-ttu-id="f9194-150">Protobuf изначально не поддерживает тип .NET `decimal`, просто `double` и `float`.</span><span class="sxs-lookup"><span data-stu-id="f9194-150">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="f9194-151">В проекте protobuf имеется текущее обсуждение о возможности добавления стандартного десятичного типа к хорошо известным типам с реализацией поддержки платформы для языков и сред, поддерживающих эту возможность.</span><span class="sxs-lookup"><span data-stu-id="f9194-151">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the well-known types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="f9194-152">Однако к настоящему моменту еще ничего не реализовано.</span><span class="sxs-lookup"><span data-stu-id="f9194-152">Nothing has been implemented yet.</span></span>

<span data-ttu-id="f9194-153">Можно создать определение сообщения, представляющее тип `decimal`, которое подходит для безопасной сериализации между клиентами и серверами .NET.</span><span class="sxs-lookup"><span data-stu-id="f9194-153">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="f9194-154">Однако разработчикам на других платформах следует понимать, какой формат используется, и реализовать свою собственную обработку.</span><span class="sxs-lookup"><span data-stu-id="f9194-154">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="f9194-155">Создание настраиваемого десятичного типа для protobuf</span><span class="sxs-lookup"><span data-stu-id="f9194-155">Creating a custom decimal type for Protobuf</span></span>

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

<span data-ttu-id="f9194-156">Поле `nanos` представляет значения из `0.999_999_999` в `-0.999_999_999`.</span><span class="sxs-lookup"><span data-stu-id="f9194-156">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="f9194-157">Например, значение `decimal` `1.5m` будет представлено как `{ units = 1, nanos = 500_000_000 }`.</span><span class="sxs-lookup"><span data-stu-id="f9194-157">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="f9194-158">Именно поэтому в поле `nanos` в этом примере используется тип `sfixed32`, который более эффективно кодируется, чем `int32` для больших значений.</span><span class="sxs-lookup"><span data-stu-id="f9194-158">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="f9194-159">Если поле `units` имеет отрицательное значение, поле `nanos` также должно быть отрицательным.</span><span class="sxs-lookup"><span data-stu-id="f9194-159">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="f9194-160">Существует несколько других алгоритмов кодирования значений `decimal` в виде строк байтов, но это сообщение проще понять, чем любое из них.</span><span class="sxs-lookup"><span data-stu-id="f9194-160">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="f9194-161">На значения не влияет обратный порядок байтов или прямой порядок байтов в разных платформах.</span><span class="sxs-lookup"><span data-stu-id="f9194-161">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="f9194-162">Преобразование между этим типом и типом BCL `decimal` может быть реализовано в C# следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f9194-162">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

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

## <a name="collections"></a><span data-ttu-id="f9194-163">Коллекции</span><span class="sxs-lookup"><span data-stu-id="f9194-163">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="f9194-164">Списки</span><span class="sxs-lookup"><span data-stu-id="f9194-164">Lists</span></span>

<span data-ttu-id="f9194-165">Списки в protobuf указываются с помощью ключевого слова префикса `repeated` в поле.</span><span class="sxs-lookup"><span data-stu-id="f9194-165">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="f9194-166">В следующем примере показано, как создать список.</span><span class="sxs-lookup"><span data-stu-id="f9194-166">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="f9194-167">В созданном коде поля `repeated` представлены универсальным типом `Google.Protobuf.Collections.RepeatedField<T>`.</span><span class="sxs-lookup"><span data-stu-id="f9194-167">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="f9194-168">Объект `RepeatedField<T>` реализует интерфейс <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="f9194-168">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="f9194-169">Поэтому можно использовать запросы LINQ или преобразовать их в массив или список.</span><span class="sxs-lookup"><span data-stu-id="f9194-169">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="f9194-170">У свойств `RepeatedField<T>` нет открытого метода задания.</span><span class="sxs-lookup"><span data-stu-id="f9194-170">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="f9194-171">Элементы должны быть добавлены в существующую коллекцию.</span><span class="sxs-lookup"><span data-stu-id="f9194-171">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="f9194-172">Словари</span><span class="sxs-lookup"><span data-stu-id="f9194-172">Dictionaries</span></span>

<span data-ttu-id="f9194-173">Тип .NET <xref:System.Collections.Generic.IDictionary%602> представлен в protobuf с помощью `map<key_type, value_type>`.</span><span class="sxs-lookup"><span data-stu-id="f9194-173">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="f9194-174">В созданном коде .NET поля `map` представлены универсальным типом `Google.Protobuf.Collections.MapField<TKey, TValue>`.</span><span class="sxs-lookup"><span data-stu-id="f9194-174">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="f9194-175">Объект `MapField<TKey, TValue>` реализует интерфейс <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="f9194-175">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="f9194-176">Как и свойства `repeated`, свойства `map` не имеют открытого метода задания.</span><span class="sxs-lookup"><span data-stu-id="f9194-176">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="f9194-177">Элементы должны быть добавлены в существующую коллекцию.</span><span class="sxs-lookup"><span data-stu-id="f9194-177">Items should be added to the existing collection.</span></span>

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

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="f9194-178">Неструктурированные и условные сообщения</span><span class="sxs-lookup"><span data-stu-id="f9194-178">Unstructured and conditional messages</span></span>

<span data-ttu-id="f9194-179">Protobuf — это формат обмена сообщениями с учетом контрактов, и при создании приложения необходимо указать сообщения приложений в файле *.proto*.</span><span class="sxs-lookup"><span data-stu-id="f9194-179">Protobuf is a contract-first messaging format, and an apps messages need to be specified in *.proto* files when the app is built.</span></span> <span data-ttu-id="f9194-180">Для расширенных сценариев protobuf предлагает функции языка и хорошо известные типы для поддержки условных и неизвестных сообщений.</span><span class="sxs-lookup"><span data-stu-id="f9194-180">For advanced scenarios, Protobuf offers language features and well known types to support conditional and unknown messages.</span></span>

### <a name="any"></a><span data-ttu-id="f9194-181">Любой</span><span class="sxs-lookup"><span data-stu-id="f9194-181">Any</span></span>

<span data-ttu-id="f9194-182">Тип `Any` позволяет использовать сообщения как внедренные типы без определения *.proto*.</span><span class="sxs-lookup"><span data-stu-id="f9194-182">The `Any` type lets you use messages as embedded types without having their *.proto* definition.</span></span> <span data-ttu-id="f9194-183">Чтобы использовать тип `Any`, импортируйте `any.proto`.</span><span class="sxs-lookup"><span data-stu-id="f9194-183">To use the `Any` type, import `any.proto`.</span></span>

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

### <a name="oneof"></a><span data-ttu-id="f9194-184">Oneof</span><span class="sxs-lookup"><span data-stu-id="f9194-184">Oneof</span></span>

<span data-ttu-id="f9194-185">Поля `oneof` — это функция языка.</span><span class="sxs-lookup"><span data-stu-id="f9194-185">`oneof` fields are a language feature.</span></span> <span data-ttu-id="f9194-186">Компилятор обрабатывает ключевое слово `oneof` при формировании класса сообщения.</span><span class="sxs-lookup"><span data-stu-id="f9194-186">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="f9194-187">Использование `oneof` для указания ответного сообщения, которое может возвращать `Person` или `Error`, может выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="f9194-187">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

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

<span data-ttu-id="f9194-188">Поля в наборе `oneof` должны иметь уникальные номера полей в общем объявлении сообщения.</span><span class="sxs-lookup"><span data-stu-id="f9194-188">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="f9194-189">При использовании `oneof` созданный код C# включает перечисление, указывающее, какое из полей было задано.</span><span class="sxs-lookup"><span data-stu-id="f9194-189">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="f9194-190">Можно проверить перечисление, чтобы узнать, какое поле задается.</span><span class="sxs-lookup"><span data-stu-id="f9194-190">You can test the enum to find which field is set.</span></span> <span data-ttu-id="f9194-191">Поля, которые не заданы, возвращают `null` или значение по умолчанию вместо создания исключения.</span><span class="sxs-lookup"><span data-stu-id="f9194-191">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = client.GetPersonAsync(new RequestMessage());

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

### <a name="value"></a><span data-ttu-id="f9194-192">Значение</span><span class="sxs-lookup"><span data-stu-id="f9194-192">Value</span></span>

<span data-ttu-id="f9194-193">Тип `Value` представляет динамически типизированное значение.</span><span class="sxs-lookup"><span data-stu-id="f9194-193">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="f9194-194">Это может быть либо `null`, число, строка, логическое значение, словарь значений (`Struct`), либо список значений (`ValueList`).</span><span class="sxs-lookup"><span data-stu-id="f9194-194">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="f9194-195">`Value` — это хорошо известный тип, использующий описанную выше функцию `oneof`.</span><span class="sxs-lookup"><span data-stu-id="f9194-195">`Value` is a well known type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="f9194-196">Чтобы использовать тип `Value`, импортируйте `struct.proto`.</span><span class="sxs-lookup"><span data-stu-id="f9194-196">To use the `Value` type, import `struct.proto`.</span></span>

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

<span data-ttu-id="f9194-197">Использование `Value` напрямую может быть подробным.</span><span class="sxs-lookup"><span data-stu-id="f9194-197">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="f9194-198">Альтернативный способ использования `Value` — встроенная поддержка protobuf для сопоставления сообщений с JSON.</span><span class="sxs-lookup"><span data-stu-id="f9194-198">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="f9194-199">Типы `JsonFormatter` и `JsonWriter` protobuf можно использовать с любым сообщением protobuf.</span><span class="sxs-lookup"><span data-stu-id="f9194-199">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="f9194-200">`Value` особенно хорошо подходит для преобразования в JSON и обратно.</span><span class="sxs-lookup"><span data-stu-id="f9194-200">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="f9194-201">Это эквивалент JSON предыдущего кода.</span><span class="sxs-lookup"><span data-stu-id="f9194-201">This is the JSON equivalent of the previous code:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f9194-202">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f9194-202">Additional resources</span></span>

* [<span data-ttu-id="f9194-203">Руководство по языку protobuf</span><span class="sxs-lookup"><span data-stu-id="f9194-203">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
