---
title: Создание служб и методов gRPC
author: jamesnk
description: Узнайте, как создавать службы и методы gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058744"
---
# <a name="create-grpc-services-and-methods"></a>Создание служб и методов gRPC

Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)

В этом документе объясняется, как создать службы и методы gRPC в C#. Будут рассмотрены следующие задачи:

* Определение служб и методов в файлах *.proto*.
* Код, созданный с помощью инструментов gRPC в C#.
* Реализация служб и методов gRPC.

## <a name="create-new-grpc-services"></a>Создание новых служб gRPC

[Службы gRPC на языке C#](xref:grpc/basics) предоставляют подход к разработке API на основе контракта gRPC. Службы и сообщения определяются в файлах *.proto*. Затем средства C# создают код из файлов *.proto*. Для ресурсов на стороне сервера абстрактный базовый тип создается для каждой службы вместе с классами для любых сообщений.

Следующий файл *.proto*:

* Определяет службу `Greeter`.
* Служба `Greeter` определяет вызов `SayHello`.
* `SayHello` отправляет сообщение `HelloRequest` и получает сообщение `HelloReply`.

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

Средства C# создают базовый тип C# `GreeterBase`.

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

По умолчанию созданный `GreeterBase` не выполняет никаких действий. Его виртуальный метод `SayHello` возвращает ошибку `UNIMPLEMENTED` для всех клиентов, которые ее вызывают. Чтобы службу можно было использовать, приложение должно создать конкретную реализацию `GreeterBase`.

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

Реализация службы регистрируется в приложении. Если служба размещена ASP.NET Core gRPC, ее следует добавить в конвейер маршрутизации с помощью метода `MapGrpcService`.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Дополнительные сведения см. в разделе <xref:grpc/aspnetcore>.

## <a name="implement-grpc-methods"></a>Реализация методов gRPC

Служба gRPC может иметь различные типы методов. Способ отправки и получения сообщений службой зависит от типа определенного метода. Типы методов gRPC:

* Унарный
* Потоковая передача сервера
* Потоковая передача клиента
* Двунаправленная потоковая передача

Потоковые вызовы указываются с помощью ключевого слова `stream` в файле *.proto*. `stream` можно поместить в сообщение запроса вызова, ответное сообщение или и в то и в другое.

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

Каждый тип вызова имеет разную сигнатуру метода. Переопределение созданных методов из абстрактного базового типа службы в конкретной реализации гарантирует, что используются правильные аргументы и возвращаемый тип.

### <a name="unary-method"></a>Унарный метод

Унарный метод получает сообщение запроса в качестве параметра и возвращает ответ. При возвращении ответа будет выполнен унарный вызов.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

Унарные вызовы больше всего похожи на [действия на контроллерах веб-API](xref:web-api/index). Одно важное отличие от gRPC-методов заключается в том, что методы gRPC не могут привязывать части запроса к разным аргументам метода. Методы gRPC всегда имеют один аргумент message для данных входящего запроса. Несколько значений по-прежнему можно отправить в службу gRPC, сделав их полями в сообщении запроса.

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Метод потоковой передачи сервера

Метод потоковой передачи сервера получает сообщение запроса в виде параметра. Так как несколько сообщений можно передать обратно вызывающему объекту, для отправки ответных сообщений используется `responseStream.WriteAsync`. Вызов потоковой передачи сервера завершается, когда метод возвращает результат.

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

Клиент не может отправить дополнительные сообщения или данные после запуска метода потоковой передачи сервера. Некоторые методы потоковой передачи предназначены для непрерывного выполнения. Для методов непрерывной потоковой передачи клиент может отменить вызов, если он больше не нужен. При отмене клиент отправляет сигнал на сервер и вызывается [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken). Токен `CancellationToken` должен использоваться на сервере с асинхронными методами, чтобы:

* любая асинхронная работа отменялась вместе с вызовом потоковой передачи;
* метод быстро завершал свою работу.

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

### <a name="client-streaming-method"></a>Метод потоковой передачи клиента

Метод потоковой передачи клиента запускается *без* метода, получающего сообщение. Параметр `requestStream` используется для чтения сообщений от клиента. Метод потоковой передачи клиента завершается при возвращении ответного сообщения.

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

Если используется C# 8 или более поздней версии, для чтения сообщений можно использовать синтаксис `await foreach`. Метод расширения `IAsyncStreamReader<T>.ReadAllAsync()` считывает все сообщения из потока запросов.

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

### <a name="bi-directional-streaming-method"></a>Метод двунаправленной потоковой передачи

Метод двунаправленной потоковой передачи запускается *без* метода, получающего сообщение. Параметр `requestStream` используется для чтения сообщений от клиента. Клиент может выбрать отправку сообщений с помощью `responseStream.WriteAsync`. Метод двунаправленной потоковой передачи завершается, когда метод возвращает:

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

Предыдущий код:

* Отправляет ответ для каждого запроса.
* Является базовым сценарием использования двунаправленной потоковой передачи.

Существует возможность поддержки более сложных сценариев, таких как чтение запросов и отправка ответов одновременно.

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

В методе двунаправленной потоковой передачи клиент и служба могут обмениваться сообщениями в любое время. Оптимальная реализация двунаправленного метода зависит от требований.

## <a name="access-grpc-request-headers"></a>Доступ к заголовкам запроса gRPC

Сообщение запроса — не единственный способ отправки данных клиентом в службу gRPC. Значения заголовков доступны в службе с помощью `ServerCallContext.RequestHeaders`.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:grpc/basics>
* <xref:grpc/client>
