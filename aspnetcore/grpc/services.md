---
title: Создание служб и методов gRPC
author: jamesnk
description: Узнайте, как создавать службы и методы gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: fde589b2de9d908db26a2557c5f57c715625aadc
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945515"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="bfa5f-103">Создание служб и методов gRPC</span><span class="sxs-lookup"><span data-stu-id="bfa5f-103">Create gRPC services and methods</span></span>

<span data-ttu-id="bfa5f-104">В этом документе объясняется, как создать службы и методы gRPC в C#.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-104">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="bfa5f-105">Будут рассмотрены следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="bfa5f-105">Topics include:</span></span>

* <span data-ttu-id="bfa5f-106">Определение служб и методов в файлах *.proto*.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-106">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="bfa5f-107">Код, созданный с помощью инструментов gRPC в C#.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-107">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="bfa5f-108">Реализация служб и методов gRPC.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-108">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="bfa5f-109">Создание новых служб gRPC</span><span class="sxs-lookup"><span data-stu-id="bfa5f-109">Create new gRPC services</span></span>

<span data-ttu-id="bfa5f-110">[Службы gRPC на языке C#](xref:grpc/basics) предоставляют подход к разработке API на основе контракта gRPC.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-110">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="bfa5f-111">Службы и сообщения определяются в файлах *.proto*.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-111">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="bfa5f-112">Затем средства C# создают код из файлов *.proto*.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-112">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="bfa5f-113">Для ресурсов на стороне сервера абстрактный базовый тип создается для каждой службы вместе с классами для любых сообщений.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-113">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="bfa5f-114">Следующий файл *.proto*:</span><span class="sxs-lookup"><span data-stu-id="bfa5f-114">The following *.proto* file:</span></span>

* <span data-ttu-id="bfa5f-115">Определяет службу `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-115">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="bfa5f-116">Служба `Greeter` определяет вызов `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-116">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="bfa5f-117">`SayHello` отправляет сообщение `HelloRequest` и получает сообщение `HelloReply`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-117">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="bfa5f-118">Средства C# создают базовый тип C# `GreeterBase`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-118">C# tooling generates the C# `GreeterBase` base type:</span></span>

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="bfa5f-119">По умолчанию созданный `GreeterBase` не выполняет никаких действий.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-119">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="bfa5f-120">Его виртуальный метод `SayHello` возвращает ошибку `UNIMPLEMENTED` для всех клиентов, которые ее вызывают.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-120">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="bfa5f-121">Чтобы службу можно было использовать, приложение должно создать конкретную реализацию `GreeterBase`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-121">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="bfa5f-122">Реализация службы регистрируется в приложении.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-122">The service implementation is registered with the app.</span></span> <span data-ttu-id="bfa5f-123">Если служба размещена ASP.NET Core gRPC, ее следует добавить в конвейер маршрутизации с помощью метода `MapGrpcService`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-123">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="bfa5f-124">Дополнительные сведения см. в разделе <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-124">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="bfa5f-125">Реализация методов gRPC</span><span class="sxs-lookup"><span data-stu-id="bfa5f-125">Implement gRPC methods</span></span>

<span data-ttu-id="bfa5f-126">Служба gRPC может иметь различные типы методов.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-126">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="bfa5f-127">Способ отправки и получения сообщений службой зависит от типа определенного метода.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-127">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="bfa5f-128">Типы методов gRPC:</span><span class="sxs-lookup"><span data-stu-id="bfa5f-128">The gRPC method types are:</span></span>

* <span data-ttu-id="bfa5f-129">Унарный</span><span class="sxs-lookup"><span data-stu-id="bfa5f-129">Unary</span></span>
* <span data-ttu-id="bfa5f-130">Потоковая передача сервера</span><span class="sxs-lookup"><span data-stu-id="bfa5f-130">Server streaming</span></span>
* <span data-ttu-id="bfa5f-131">Потоковая передача клиента</span><span class="sxs-lookup"><span data-stu-id="bfa5f-131">Client streaming</span></span>
* <span data-ttu-id="bfa5f-132">Двунаправленная потоковая передача</span><span class="sxs-lookup"><span data-stu-id="bfa5f-132">Bi-directional streaming</span></span>

<span data-ttu-id="bfa5f-133">Потоковые вызовы указываются с помощью ключевого слова `stream` в файле *.proto*.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-133">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="bfa5f-134">`stream` можно поместить в сообщение запроса вызова, ответное сообщение или и в то и в другое.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-134">`stream` can be placed on a call's request message, response message, or both.</span></span>

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

<span data-ttu-id="bfa5f-135">Каждый тип вызова имеет разную сигнатуру метода.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-135">Each call type has a different method signature.</span></span> <span data-ttu-id="bfa5f-136">Переопределение созданных методов из абстрактного базового типа службы в конкретной реализации гарантирует, что используются правильные аргументы и возвращаемый тип.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-136">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="bfa5f-137">Унарный метод</span><span class="sxs-lookup"><span data-stu-id="bfa5f-137">Unary method</span></span>

<span data-ttu-id="bfa5f-138">Унарный метод получает сообщение запроса в качестве параметра и возвращает ответ.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-138">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="bfa5f-139">При возвращении ответа будет выполнен унарный вызов.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-139">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="bfa5f-140">Унарные вызовы больше всего похожи на [действия на контроллерах веб-API](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="bfa5f-140">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="bfa5f-141">Одно важное отличие от gRPC-методов заключается в том, что методы gRPC не могут привязывать части запроса к разным аргументам метода.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-141">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="bfa5f-142">Методы gRPC всегда имеют один аргумент message для данных входящего запроса.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-142">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="bfa5f-143">Несколько значений по-прежнему можно отправить в службу gRPC, сделав их полями в сообщении запроса.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-143">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="bfa5f-144">Метод потоковой передачи сервера</span><span class="sxs-lookup"><span data-stu-id="bfa5f-144">Server streaming method</span></span>

<span data-ttu-id="bfa5f-145">Метод потоковой передачи сервера получает сообщение запроса в виде параметра.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-145">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="bfa5f-146">Так как несколько сообщений можно передать обратно вызывающему объекту, для отправки ответных сообщений используется `responseStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-146">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="bfa5f-147">Вызов потоковой передачи сервера завершается, когда метод возвращает результат.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-147">A server streaming call is complete when the method returns.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

<span data-ttu-id="bfa5f-148">Клиент не может отправить дополнительные сообщения или данные после запуска метода потоковой передачи сервера.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-148">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="bfa5f-149">Некоторые методы потоковой передачи предназначены для непрерывного выполнения.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-149">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="bfa5f-150">Для методов непрерывной потоковой передачи клиент может отменить вызов, если он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-150">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="bfa5f-151">При отмене клиент отправляет сигнал на сервер и вызывается [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken).</span><span class="sxs-lookup"><span data-stu-id="bfa5f-151">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="bfa5f-152">Токен `CancellationToken` должен использоваться на сервере с асинхронными методами, чтобы:</span><span class="sxs-lookup"><span data-stu-id="bfa5f-152">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="bfa5f-153">любая асинхронная работа отменялась вместе с вызовом потоковой передачи;</span><span class="sxs-lookup"><span data-stu-id="bfa5f-153">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="bfa5f-154">метод быстро завершал свою работу.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-154">The method exits quickly.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a><span data-ttu-id="bfa5f-155">Метод потоковой передачи клиента</span><span class="sxs-lookup"><span data-stu-id="bfa5f-155">Client streaming method</span></span>

<span data-ttu-id="bfa5f-156">Метод потоковой передачи клиента запускается *без* метода, получающего сообщение.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-156">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="bfa5f-157">Параметр `requestStream` используется для чтения сообщений от клиента.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-157">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="bfa5f-158">Метод потоковой передачи клиента завершается при возвращении ответного сообщения.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-158">A client streaming call is complete when a response message is returned:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

<span data-ttu-id="bfa5f-159">Если используется C# 8 или более поздней версии, для чтения сообщений можно использовать синтаксис `await foreach`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-159">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="bfa5f-160">Метод расширения `IAsyncStreamReader<T>.ReadAllAsync()` считывает все сообщения из потока запросов.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-160">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="bfa5f-161">Метод двунаправленной потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="bfa5f-161">Bi-directional streaming method</span></span>

<span data-ttu-id="bfa5f-162">Метод двунаправленной потоковой передачи запускается *без* метода, получающего сообщение.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-162">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="bfa5f-163">Параметр `requestStream` используется для чтения сообщений от клиента.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-163">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="bfa5f-164">Клиент может выбрать отправку сообщений с помощью `responseStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-164">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="bfa5f-165">Метод двунаправленной потоковой передачи завершается, когда метод возвращает:</span><span class="sxs-lookup"><span data-stu-id="bfa5f-165">A bi-directional streaming call is complete when the the method returns:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

<span data-ttu-id="bfa5f-166">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="bfa5f-166">The preceding code:</span></span>

* <span data-ttu-id="bfa5f-167">Отправляет ответ для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-167">Sends a response for each request.</span></span>
* <span data-ttu-id="bfa5f-168">Является базовым сценарием использования двунаправленной потоковой передачи.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-168">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="bfa5f-169">Существует возможность поддержки более сложных сценариев, таких как чтение запросов и отправка ответов одновременно.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-169">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

<span data-ttu-id="bfa5f-170">В методе двунаправленной потоковой передачи клиент и служба могут обмениваться сообщениями в любое время.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-170">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="bfa5f-171">Оптимальная реализация двунаправленного метода зависит от требований.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-171">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="bfa5f-172">Доступ к заголовкам запроса gRPC</span><span class="sxs-lookup"><span data-stu-id="bfa5f-172">Access gRPC request headers</span></span>

<span data-ttu-id="bfa5f-173">Сообщение запроса — не единственный способ отправки данных клиентом в службу gRPC.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-173">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="bfa5f-174">Значения заголовков доступны в службе с помощью `ServerCallContext.RequestHeaders`.</span><span class="sxs-lookup"><span data-stu-id="bfa5f-174">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="bfa5f-175">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="bfa5f-175">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
