---
title: Рекомендации по повышению производительности gRPC для ASP.NET Core
author: jamesnk
description: Ознакомьтесь с рекомендациями по созданию высокопроизводительных служб gRPC.
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
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876728"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="228b2-103">Рекомендации по повышению производительности gRPC для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="228b2-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="228b2-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="228b2-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="228b2-105">Система gRPC предназначена для создания высокопроизводительных служб.</span><span class="sxs-lookup"><span data-stu-id="228b2-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="228b2-106">В этом документе описывается, как обеспечить максимальную производительность gRPC.</span><span class="sxs-lookup"><span data-stu-id="228b2-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="228b2-107">Повторное использование канала</span><span class="sxs-lookup"><span data-stu-id="228b2-107">Reuse channel</span></span>

<span data-ttu-id="228b2-108">При выполнении вызовов gRPC канал gRPC следует использовать повторно.</span><span class="sxs-lookup"><span data-stu-id="228b2-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="228b2-109">Повторное использование канала позволяет мультиплексировать вызовы через существующее соединение HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="228b2-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="228b2-110">Если для каждого вызова gRPC создается новый канал, то время, необходимое для его выполнения, может значительно возрасти.</span><span class="sxs-lookup"><span data-stu-id="228b2-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="228b2-111">Каждый вызов потребует создания нескольких круговых путей по сети между клиентом и сервером для создания соединения HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="228b2-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="228b2-112">открытие сокета;</span><span class="sxs-lookup"><span data-stu-id="228b2-112">Opening a socket</span></span>
2. <span data-ttu-id="228b2-113">установка TCP-соединения;</span><span class="sxs-lookup"><span data-stu-id="228b2-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="228b2-114">согласование TLS;</span><span class="sxs-lookup"><span data-stu-id="228b2-114">Negotiating TLS</span></span>
4. <span data-ttu-id="228b2-115">инициация соединения HTTP/2;</span><span class="sxs-lookup"><span data-stu-id="228b2-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="228b2-116">выполнение вызова gRPC.</span><span class="sxs-lookup"><span data-stu-id="228b2-116">Making the gRPC call</span></span>

<span data-ttu-id="228b2-117">Каналы можно безопасно использовать для нескольких вызовов gRPC.</span><span class="sxs-lookup"><span data-stu-id="228b2-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="228b2-118">Клиенты gRPC создаются с помощью каналов.</span><span class="sxs-lookup"><span data-stu-id="228b2-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="228b2-119">Клиенты gRPC являются облегченными объектами и не нуждаются в кэшировании или повторном использовании.</span><span class="sxs-lookup"><span data-stu-id="228b2-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="228b2-120">Из одного канала можно создать несколько клиентов gRPC, включая различные типы клиентов.</span><span class="sxs-lookup"><span data-stu-id="228b2-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="228b2-121">Канал и клиенты, созданные из канала, могут безопасно использоваться несколькими потоками.</span><span class="sxs-lookup"><span data-stu-id="228b2-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="228b2-122">Клиенты, созданные из канала, могут выполнять несколько одновременных вызовов.</span><span class="sxs-lookup"><span data-stu-id="228b2-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="228b2-123">Параллелизм соединений</span><span class="sxs-lookup"><span data-stu-id="228b2-123">Connection concurrency</span></span>

<span data-ttu-id="228b2-124">Обычно существует ограничение на [количество параллельных потоков (активных HTTP-запросов)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) для одного соединения по HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="228b2-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="228b2-125">По умолчанию на большинстве серверов оно составляет 100 параллельных потоков.</span><span class="sxs-lookup"><span data-stu-id="228b2-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="228b2-126">Канал gRPC использует одно соединение HTTP/2, параллельные вызовы по которому мультиплексируются.</span><span class="sxs-lookup"><span data-stu-id="228b2-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="228b2-127">Когда число активных вызовов достигает предельного числа потоков для соединения, дополнительные вызовы помещаются в очередь в клиенте.</span><span class="sxs-lookup"><span data-stu-id="228b2-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="228b2-128">Вызовы в очереди ожидают завершения активных вызовов.</span><span class="sxs-lookup"><span data-stu-id="228b2-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="228b2-129">Приложения с высокой нагрузкой или длительными потоковыми вызовами gRPC могут испытывать проблемы с производительностью, вызванные помещением вызовов в очередь из-за этого ограничения.</span><span class="sxs-lookup"><span data-stu-id="228b2-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="228b2-130">В .NET 5 появилось свойство `SocketsHttpHandler.EnableMultipleHttp2Connections`.</span><span class="sxs-lookup"><span data-stu-id="228b2-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="228b2-131">Если ему присвоено значение `true`, то при достижении предельного числа параллельных потоков канал создает дополнительные соединения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="228b2-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="228b2-132">При создании `GrpcChannel` его внутренний обработчик `SocketsHttpHandler` автоматически настраивается так, чтобы создавались дополнительные соединения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="228b2-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="228b2-133">Если приложение настраивает собственный обработчик, рекомендуется присвоить свойству `EnableMultipleHttp2Connections` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="228b2-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="228b2-134">Для приложений .NET Core 3.1 существует несколько обходных путей.</span><span class="sxs-lookup"><span data-stu-id="228b2-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="228b2-135">Создайте отдельные каналы gRPC для частей приложения с высокой нагрузкой.</span><span class="sxs-lookup"><span data-stu-id="228b2-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="228b2-136">Например, служба gRPC `Logger` может испытывать высокую нагрузку.</span><span class="sxs-lookup"><span data-stu-id="228b2-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="228b2-137">Используйте отдельный канал для создания `LoggerClient` в приложении.</span><span class="sxs-lookup"><span data-stu-id="228b2-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="228b2-138">Используйте пул каналов gRPC, например создайте их список.</span><span class="sxs-lookup"><span data-stu-id="228b2-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="228b2-139">`Random` используется для выбора канала из списка каждый раз, когда требуется канал gRPC.</span><span class="sxs-lookup"><span data-stu-id="228b2-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="228b2-140">Использование `Random` позволяет случайным образом распределять вызовы между несколькими соединениями.</span><span class="sxs-lookup"><span data-stu-id="228b2-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="228b2-141">Еще один способ решить эту проблему — увеличить максимальное число параллельных потоков на сервере.</span><span class="sxs-lookup"><span data-stu-id="228b2-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="228b2-142">В Kestrel оно настраивается с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span><span class="sxs-lookup"><span data-stu-id="228b2-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="228b2-143">Увеличивать максимальное число параллельных потоков не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="228b2-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="228b2-144">Слишком большое число потоков в одном соединении HTTP/2 вызывает новые проблемы с производительностью.</span><span class="sxs-lookup"><span data-stu-id="228b2-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="228b2-145">Возникает состязание между потоками, пытающимися выполнить запись через соединение.</span><span class="sxs-lookup"><span data-stu-id="228b2-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="228b2-146">Потеря пакетов, передаваемых через соединение, приводит к блокировке всех вызовов на уровне TCP.</span><span class="sxs-lookup"><span data-stu-id="228b2-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="228b2-147">Пакеты проверки активности</span><span class="sxs-lookup"><span data-stu-id="228b2-147">Keep alive pings</span></span>

<span data-ttu-id="228b2-148">Пакеты проверки активности могут использоваться для поддержания соединений HTTP/2 в активном состоянии в периоды бездействия.</span><span class="sxs-lookup"><span data-stu-id="228b2-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="228b2-149">Готовность соединения HTTP/2 на момент возобновления работы приложения позволяет быстро выполнять первые вызовы gRPC без задержки, вызванной повторным установлением соединения.</span><span class="sxs-lookup"><span data-stu-id="228b2-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="228b2-150">Пакеты проверки активности настраиваются в <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="228b2-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="228b2-151">Приведенный выше код настраивает канал, который отправляет пакет проверки активности на сервер каждые 60 секунд в периоды бездействия.</span><span class="sxs-lookup"><span data-stu-id="228b2-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="228b2-152">Проверка активности гарантирует, что сервер и все используемые прокси-серверы не закроют соединение из-за бездействия.</span><span class="sxs-lookup"><span data-stu-id="228b2-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="228b2-153">Потоковая передача</span><span class="sxs-lookup"><span data-stu-id="228b2-153">Streaming</span></span>

<span data-ttu-id="228b2-154">В высокопроизводительных сценариях вместо отдельных вызовов gRPC может использоваться двунаправленная потоковая передача gRPC.</span><span class="sxs-lookup"><span data-stu-id="228b2-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="228b2-155">После запуска двунаправленного потока потоковая передача сообщений в обе стороны происходит быстрее, чем отправка сообщений для нескольких отдельных вызовов gRPC.</span><span class="sxs-lookup"><span data-stu-id="228b2-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="228b2-156">Потоковые сообщения отправляются в виде данных существующего запроса HTTP/2, благодаря чему устраняются издержки на создание запроса HTTP/2 для каждого отдельного вызова.</span><span class="sxs-lookup"><span data-stu-id="228b2-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="228b2-157">Пример службы:</span><span class="sxs-lookup"><span data-stu-id="228b2-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="228b2-158">Пример клиента:</span><span class="sxs-lookup"><span data-stu-id="228b2-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="228b2-159">Замена отдельных вызовов на двунаправленную потоковую передачу для повышения производительности — сложный в реализации метод, который не подходит во многих ситуациях.</span><span class="sxs-lookup"><span data-stu-id="228b2-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="228b2-160">Использовать потоковые вызовы рекомендуется в указанных ниже случаях.</span><span class="sxs-lookup"><span data-stu-id="228b2-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="228b2-161">Требуется высокая пропускная способность или низкая задержка.</span><span class="sxs-lookup"><span data-stu-id="228b2-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="228b2-162">gRPC и HTTP/2 были определены как узкие места производительности.</span><span class="sxs-lookup"><span data-stu-id="228b2-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="228b2-163">Рабочая роль клиента регулярно отправляет или получает сообщения с помощью службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="228b2-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="228b2-164">Обратите внимание на дополнительные сложности и ограничения, связанные с использованием потоковых вызовов вместо отдельных.</span><span class="sxs-lookup"><span data-stu-id="228b2-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="228b2-165">Поток может быть прерван службой или ошибкой соединения.</span><span class="sxs-lookup"><span data-stu-id="228b2-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="228b2-166">Для перезапуска потока при возникновении ошибки требуется логика.</span><span class="sxs-lookup"><span data-stu-id="228b2-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="228b2-167">`RequestStream.WriteAsync` небезопасно использовать в режиме многопоточности.</span><span class="sxs-lookup"><span data-stu-id="228b2-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="228b2-168">В поток за раз можно записать только одно сообщение.</span><span class="sxs-lookup"><span data-stu-id="228b2-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="228b2-169">Для отправки сообщений из нескольких потоков требуется очередь производителя или потребителя, например <xref:System.Threading.Channels.Channel%601>, для маршалирования сообщений.</span><span class="sxs-lookup"><span data-stu-id="228b2-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="228b2-170">Метод потоковой передачи gRPC ограничен получением и отправкой сообщений одного типа.</span><span class="sxs-lookup"><span data-stu-id="228b2-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="228b2-171">Например, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` получает `RequestMessage` и отправляет `ResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="228b2-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="228b2-172">Поддержка неизвестных или условных сообщений в protobuf благодаря `Any` и `oneof` позволяет обойти это ограничение.</span><span class="sxs-lookup"><span data-stu-id="228b2-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
