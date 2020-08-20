---
title: Интеграция производства клиента gRPC в .NET Core
author: jamesnk
description: Узнайте, как создавать клиенты gRPC с помощью производства клиента.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/clientfactory
ms.openlocfilehash: dfdcb8d73017c0c1ccb4cf2aaffdbe7c49030179
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633738"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="915cd-103">Интеграция производства клиента gRPC в .NET Core</span><span class="sxs-lookup"><span data-stu-id="915cd-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="915cd-104">Интеграция gRPC с `HttpClientFactory` обеспечивает централизованный способ создания клиентов gRPC.</span><span class="sxs-lookup"><span data-stu-id="915cd-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="915cd-105">Его можно использовать в качестве альтернативы [настройке отдельных экземпляров клиента gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="915cd-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="915cd-106">Интеграция производства доступна в пакете NuGet [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory).</span><span class="sxs-lookup"><span data-stu-id="915cd-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="915cd-107">Производство обеспечивает следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="915cd-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="915cd-108">предоставляет центральное расположение для настройки логических экземпляров клиента gRPC;</span><span class="sxs-lookup"><span data-stu-id="915cd-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="915cd-109">управляет временем существования базового объекта `HttpClientMessageHandler`;</span><span class="sxs-lookup"><span data-stu-id="915cd-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="915cd-110">автоматически распространяет крайний срок и отмену в службе gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="915cd-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="915cd-111">Регистрация клиентов gRPC</span><span class="sxs-lookup"><span data-stu-id="915cd-111">Register gRPC clients</span></span>

<span data-ttu-id="915cd-112">Для регистрации клиента gRPC можно использовать универсальный метод расширения `AddGrpcClient` в `Startup.ConfigureServices`, указав класс типизированного клиента gRPC и адрес службы:</span><span class="sxs-lookup"><span data-stu-id="915cd-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="915cd-113">Тип клиента gRPC регистрируется в системе внедрения зависимостей (DI) как временный.</span><span class="sxs-lookup"><span data-stu-id="915cd-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="915cd-114">После этого клиент можно внедрять и использовать напрямую в типах, создаваемых посредством внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="915cd-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="915cd-115">Автоматически внедрять клиенты gRPC можно в контроллеры MVC ASP.NET Core, концентраторы SignalR и службы gRPC:</span><span class="sxs-lookup"><span data-stu-id="915cd-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="915cd-116">Настройка HttpClient</span><span class="sxs-lookup"><span data-stu-id="915cd-116">Configure HttpClient</span></span>

<span data-ttu-id="915cd-117">`HttpClientFactory` создает объект `HttpClient`, используемый клиентом gRPC.</span><span class="sxs-lookup"><span data-stu-id="915cd-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="915cd-118">С помощью стандартных методов `HttpClientFactory` можно добавлять ПО промежуточного слоя для исходящих запросов или настраивать базовый обработчик `HttpClientHandler` объекта `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="915cd-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="915cd-119">Дополнительные сведения см. в статье [Выполнение HTTP-запросов с помощью IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="915cd-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="915cd-120">Настройка канала и перехватчиков</span><span class="sxs-lookup"><span data-stu-id="915cd-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="915cd-121">Для gRPC доступны специальные методы, позволяющие делать следующее:</span><span class="sxs-lookup"><span data-stu-id="915cd-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="915cd-122">настраивать базовый канал клиента gRPC;</span><span class="sxs-lookup"><span data-stu-id="915cd-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="915cd-123">добавлять экземпляры `Interceptor`, которые клиент будет использовать при выполнении вызовов gRPC.</span><span class="sxs-lookup"><span data-stu-id="915cd-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="915cd-124">Распространение крайнего срока и отмены</span><span class="sxs-lookup"><span data-stu-id="915cd-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="915cd-125">С помощью метода `EnableCallContextPropagation()` для клиентов gRPC, созданных производством в службе gRPC, можно настроить автоматическое распространение токена крайнего срока и отмены на дочерние вызовы.</span><span class="sxs-lookup"><span data-stu-id="915cd-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="915cd-126">Метод расширения `EnableCallContextPropagation()` доступен в пакете NuGet [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory).</span><span class="sxs-lookup"><span data-stu-id="915cd-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="915cd-127">Распространение контекста вызова осуществляется путем считывания токена крайнего срока и отмены из текущего контекста запроса gRPC и его автоматического распространения на исходящие вызовы, выполняемые клиентом gRPC.</span><span class="sxs-lookup"><span data-stu-id="915cd-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="915cd-128">Распространение контекста вызова — это отличный способ обеспечить распространение крайнего срока и отмены в случае со сложными вложенными вызовами gRPC.</span><span class="sxs-lookup"><span data-stu-id="915cd-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="915cd-129">По умолчанию `EnableCallContextPropagation` вызывает ошибку, если клиент используется вне контекста вызова gRPC.</span><span class="sxs-lookup"><span data-stu-id="915cd-129">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="915cd-130">Эта ошибка информирует о том, что контекст вызова для распространения отсутствует.</span><span class="sxs-lookup"><span data-stu-id="915cd-130">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="915cd-131">Если вы хотите использовать клиент за пределами контекста вызова, подавите ошибку при настройке клиента с использованием `SuppressContextNotFoundErrors`:</span><span class="sxs-lookup"><span data-stu-id="915cd-131">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="915cd-132">Дополнительные сведения о крайних сроках и отмене RPC см. в разделе, посвященном [жизненному циклу RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="915cd-132">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="915cd-133">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="915cd-133">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
