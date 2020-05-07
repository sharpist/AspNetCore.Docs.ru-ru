---
title: Вызов служб gRPC с помощью клиента .NET
author: jamesnk
description: Узнайте, как вызывать службы gRPC с помощью клиента gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: c554ce9702a9f2b2efeabbfdf0d1319ca4161a1c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774734"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="dce01-103">Вызов служб gRPC с помощью клиента .NET</span><span class="sxs-lookup"><span data-stu-id="dce01-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="dce01-104">Клиентская библиотека .NET gRPC доступна в пакете NuGet [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client).</span><span class="sxs-lookup"><span data-stu-id="dce01-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="dce01-105">В этом документе объясняется, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="dce01-105">This document explains how to:</span></span>

* <span data-ttu-id="dce01-106">Настройка клиента gRPC для вызова служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="dce01-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="dce01-107">Вызовы gRPC для унарного метода, методов потоковой передачи сервера, потоковой передачи клиента и двунаправленной потоковой передачи.</span><span class="sxs-lookup"><span data-stu-id="dce01-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="dce01-108">Настройка клиента gRPC</span><span class="sxs-lookup"><span data-stu-id="dce01-108">Configure gRPC client</span></span>

<span data-ttu-id="dce01-109">Клиенты gRPC являются конкретными типами клиентов, [создаваемыми в файлах *\*.proto*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="dce01-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="dce01-110">Конкретный клиент gRPC использует методы, которые выполняют преобразование для служб gRPC в файле *\*.proto*.</span><span class="sxs-lookup"><span data-stu-id="dce01-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="dce01-111">Клиент gRPC создается из канала.</span><span class="sxs-lookup"><span data-stu-id="dce01-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="dce01-112">Для начала воспользуйтесь `GrpcChannel.ForAddress`, чтобы создать канал, а затем используйте канал для создания клиента gRPC:</span><span class="sxs-lookup"><span data-stu-id="dce01-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="dce01-113">Канал представляет собой долгосрочное подключение к службе gRPC.</span><span class="sxs-lookup"><span data-stu-id="dce01-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="dce01-114">При создании канала он настраивается с параметрами, связанными с вызовом службы.</span><span class="sxs-lookup"><span data-stu-id="dce01-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="dce01-115">Например, `HttpClient`, используемый для выполнения вызовов, максимальный размер сообщения для отправки и получения, а также ведение журнала можно указать в `GrpcChannelOptions` и использовать с `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="dce01-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="dce01-116">Полный список параметров см. в разделе, [посвященном параметрам конфигурации клиента](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="dce01-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="dce01-117">Производительность и использование канала и клиента:</span><span class="sxs-lookup"><span data-stu-id="dce01-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="dce01-118">Создание канала может потребовать значительных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="dce01-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="dce01-119">Повторное использование канала для вызовов gRPC обеспечивает выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="dce01-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="dce01-120">Клиенты gRPC создаются с помощью каналов.</span><span class="sxs-lookup"><span data-stu-id="dce01-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="dce01-121">Клиенты gRPC являются облегченными объектами и не нуждаются в кэшировании или повторном использовании.</span><span class="sxs-lookup"><span data-stu-id="dce01-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="dce01-122">Из одного канала можно создать несколько клиентов gRPC, включая различные типы клиентов.</span><span class="sxs-lookup"><span data-stu-id="dce01-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="dce01-123">Канал и клиенты, созданные из канала, могут безопасно использоваться несколькими потоками.</span><span class="sxs-lookup"><span data-stu-id="dce01-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="dce01-124">Клиенты, созданные из канала, могут выполнять несколько одновременных вызовов.</span><span class="sxs-lookup"><span data-stu-id="dce01-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="dce01-125">`GrpcChannel.ForAddress` — не единственный вариант создания клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="dce01-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="dce01-126">При вызове службы gRPC из приложения ASP.NET Core, рассмотрите возможность [интеграции фабрики клиента gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="dce01-126">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="dce01-127">Интеграция gRPC с `HttpClientFactory` предлагает централизованную альтернативу созданию клиентов gRPC.</span><span class="sxs-lookup"><span data-stu-id="dce01-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="dce01-128">Для [вызова незащищенных служб gRPC с клиентом .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client) требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="dce01-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="dce01-129">Вызов gRPC через HTTP/2 с `Grpc.Net.Client` в настоящее время не поддерживается в Xamarin.</span><span class="sxs-lookup"><span data-stu-id="dce01-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="dce01-130">Мы работаем над улучшением поддержки HTTP/2 в будущих выпусках Xamarin.</span><span class="sxs-lookup"><span data-stu-id="dce01-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="dce01-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) и [gRPC-Web](xref:grpc/browser) являются приемлемыми работающими альтернативами, которые доступны на сегодняшний день.</span><span class="sxs-lookup"><span data-stu-id="dce01-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="dce01-132">Вызовы gRPC</span><span class="sxs-lookup"><span data-stu-id="dce01-132">Make gRPC calls</span></span>

<span data-ttu-id="dce01-133">Вызов gRPC инициируется путем вызова метода в клиенте.</span><span class="sxs-lookup"><span data-stu-id="dce01-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="dce01-134">Клиент gRPC будет выполнять сериализацию сообщений и направлять вызов gRPC к правильной службе.</span><span class="sxs-lookup"><span data-stu-id="dce01-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="dce01-135">gRPC имеет различные типы методов.</span><span class="sxs-lookup"><span data-stu-id="dce01-135">gRPC has different types of methods.</span></span> <span data-ttu-id="dce01-136">Способ использования клиента для выполнения вызова gRPC зависит от типа вызываемого метода.</span><span class="sxs-lookup"><span data-stu-id="dce01-136">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="dce01-137">Типы методов gRPC:</span><span class="sxs-lookup"><span data-stu-id="dce01-137">The gRPC method types are:</span></span>

* <span data-ttu-id="dce01-138">Унарный</span><span class="sxs-lookup"><span data-stu-id="dce01-138">Unary</span></span>
* <span data-ttu-id="dce01-139">Потоковая передача сервера</span><span class="sxs-lookup"><span data-stu-id="dce01-139">Server streaming</span></span>
* <span data-ttu-id="dce01-140">Потоковая передача клиента</span><span class="sxs-lookup"><span data-stu-id="dce01-140">Client streaming</span></span>
* <span data-ttu-id="dce01-141">Двунаправленная потоковая передача</span><span class="sxs-lookup"><span data-stu-id="dce01-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="dce01-142">Унарный вызов</span><span class="sxs-lookup"><span data-stu-id="dce01-142">Unary call</span></span>

<span data-ttu-id="dce01-143">Унарный вызов начинается с клиента, отправляющего сообщение с запросом.</span><span class="sxs-lookup"><span data-stu-id="dce01-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="dce01-144">После завершения работы службы возвращается ответное сообщение.</span><span class="sxs-lookup"><span data-stu-id="dce01-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="dce01-145">Каждый унарный метод службы в файле *\*PROTO* приведет к появлению двух методов .NET в конкретном типе клиента gRPC для вызова метода: асинхронного метода и блокирующего метода.</span><span class="sxs-lookup"><span data-stu-id="dce01-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="dce01-146">Например, в `GreeterClient` существует два способа вызова `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="dce01-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="dce01-147">`GreeterClient.SayHelloAsync` — асинхронный вызов службы `Greeter.SayHello`.</span><span class="sxs-lookup"><span data-stu-id="dce01-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="dce01-148">Может быть ожидаемым.</span><span class="sxs-lookup"><span data-stu-id="dce01-148">Can be awaited.</span></span>
* <span data-ttu-id="dce01-149">`GreeterClient.SayHello` — вызов службы `Greeter.SayHello` и блокировка до завершения.</span><span class="sxs-lookup"><span data-stu-id="dce01-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="dce01-150">Не используйте его в асинхронном коде.</span><span class="sxs-lookup"><span data-stu-id="dce01-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="dce01-151">Вызов потоковой передачи сервера</span><span class="sxs-lookup"><span data-stu-id="dce01-151">Server streaming call</span></span>

<span data-ttu-id="dce01-152">Вызов потоковой передачи сервера начинается с клиента, отправляющего сообщение с запросом.</span><span class="sxs-lookup"><span data-stu-id="dce01-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="dce01-153">`ResponseStream.MoveNext()` считывает сообщения, переданные в службу путем потоковой передачи.</span><span class="sxs-lookup"><span data-stu-id="dce01-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="dce01-154">Вызов потоковой передачи сервера завершается, когда `ResponseStream.MoveNext()` возвращает `false`.</span><span class="sxs-lookup"><span data-stu-id="dce01-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="dce01-155">Если используется C# 8 или более поздней версии, для чтения сообщений можно использовать синтаксис `await foreach`.</span><span class="sxs-lookup"><span data-stu-id="dce01-155">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="dce01-156">Метод расширения `IAsyncStreamReader<T>.ReadAllAsync()` считывает все сообщения из потока ответов:</span><span class="sxs-lookup"><span data-stu-id="dce01-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="dce01-157">Вызов потоковой передачи клиента</span><span class="sxs-lookup"><span data-stu-id="dce01-157">Client streaming call</span></span>

<span data-ttu-id="dce01-158">Вызов потоковой передачи клиента начинается *без* клиента, отправляющего сообщение с запросом.</span><span class="sxs-lookup"><span data-stu-id="dce01-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="dce01-159">Клиент может выбрать отправку сообщений с помощью `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="dce01-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="dce01-160">Когда клиент завершит отправку сообщений, следует вызвать `RequestStream.CompleteAsync`, чтобы уведомить службу.</span><span class="sxs-lookup"><span data-stu-id="dce01-160">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="dce01-161">Вызов завершается, когда служба возвращает ответное сообщение.</span><span class="sxs-lookup"><span data-stu-id="dce01-161">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="dce01-162">Вызов двунаправленной потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="dce01-162">Bi-directional streaming call</span></span>

<span data-ttu-id="dce01-163">Вызов двунаправленной потоковой передачи начинается *без* клиента, отправляющего сообщение с запросом.</span><span class="sxs-lookup"><span data-stu-id="dce01-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="dce01-164">Клиент может выбрать отправку сообщений с помощью `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="dce01-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="dce01-165">Сообщения, переданные в службу путем потоковой передачи, доступны с `ResponseStream.MoveNext()` или `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="dce01-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="dce01-166">Вызов двунаправленной потоковой передачи завершается, когда `ResponseStream` больше не содержит сообщений.</span><span class="sxs-lookup"><span data-stu-id="dce01-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="dce01-167">Во время вызова двунаправленной потоковой передачи клиент и служба могут обмениваться сообщениями в любое время.</span><span class="sxs-lookup"><span data-stu-id="dce01-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="dce01-168">Наиболее подходящая логика клиента для взаимодействия с вызовом двунаправленной потоковой передачи зависит от логики службы.</span><span class="sxs-lookup"><span data-stu-id="dce01-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="dce01-169">Доступ к трейлерам gRPC</span><span class="sxs-lookup"><span data-stu-id="dce01-169">Access gRPC trailers</span></span>

<span data-ttu-id="dce01-170">Вызовы gRPC могут возвращать трейлеры gRPC.</span><span class="sxs-lookup"><span data-stu-id="dce01-170">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="dce01-171">Трейлеры gRPC содержат метаданные имени и значения для вызова.</span><span class="sxs-lookup"><span data-stu-id="dce01-171">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="dce01-172">Трейлеры содержат аналогичные функции для заголовков HTTP, но они принимаются в конце вызова.</span><span class="sxs-lookup"><span data-stu-id="dce01-172">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="dce01-173">Трейлеры gRPC доступны с помощью `GetTrailers()`, которая возвращает коллекцию метаданных.</span><span class="sxs-lookup"><span data-stu-id="dce01-173">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="dce01-174">Трейлеры возвращаются после завершения ответа, поэтому перед обращением к трейлеру необходимо дождаться конца ответа.</span><span class="sxs-lookup"><span data-stu-id="dce01-174">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="dce01-175">Унарные и клиентские вызовы потоковой передачи должны дождаться `ResponseAsync` перед вызовом `GetTrailers()`:</span><span class="sxs-lookup"><span data-stu-id="dce01-175">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="dce01-176">Вызовы сервера и двунаправленной потоковой передачи должны завершить ожидание ответного потока перед вызовом `GetTrailers()`:</span><span class="sxs-lookup"><span data-stu-id="dce01-176">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="dce01-177">Трейлеры gRPC также доступны из `RpcException`.</span><span class="sxs-lookup"><span data-stu-id="dce01-177">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="dce01-178">Служба может вернуть трейлеры вместе со статусом gRPC, отличным от "ОК".</span><span class="sxs-lookup"><span data-stu-id="dce01-178">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="dce01-179">В этой ситуации трейлеры получаются из исключения, вызываемого клиентом gRPC:</span><span class="sxs-lookup"><span data-stu-id="dce01-179">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="dce01-180">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dce01-180">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
