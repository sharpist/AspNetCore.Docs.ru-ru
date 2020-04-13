---
title: конфигурация SignalR ASP.NET ядра
author: bradygaster
description: Узнайте, как настроить SignalR ASP.NET основных приложений.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223989"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="ae3d2-103">Конфигурация SignalR для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae3d2-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ae3d2-104">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ae3d2-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ae3d2-105">ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ae3d2-106">Каждый протокол имеет параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ae3d2-107">Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="ae3d2-108">`AddJsonProtocol`могут быть добавлены после `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в .</span><span class="sxs-lookup"><span data-stu-id="ae3d2-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ae3d2-109">Метод `AddJsonProtocol` принимает делегата, `options` который получает объект.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ae3d2-110">Свойство [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) на этом `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объекте является объектом, который может быть использован для настройки последовательности аргументов и значений возврата.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ae3d2-111">Для получения дополнительной [информации](/dotnet/api/system.text.json)см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="ae3d2-112">В качестве примера, чтобы настроить сериалайзер, чтобы не изменять корпус имен свойств, вместо имен "camelCase" по умолчанию, используйте следующий код в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="ae3d2-113">В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ae3d2-114">Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="ae3d2-115">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="ae3d2-116">Переключиться на Ньютонсофт.Джсон</span><span class="sxs-lookup"><span data-stu-id="ae3d2-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="ae3d2-117">Если вам нужны `Newtonsoft.Json` функции, которые `System.Text.Json`не поддерживаются в , см. [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ae3d2-118">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="ae3d2-118">MessagePack serialization options</span></span>

<span data-ttu-id="ae3d2-119">Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ae3d2-120">Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ae3d2-121">На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ae3d2-122">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="ae3d2-122">Configure server options</span></span>

<span data-ttu-id="ae3d2-123">В следующей таблице описаны варианты настройки концентраторов SignalR:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ae3d2-124">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-124">Option</span></span> | <span data-ttu-id="ae3d2-125">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-125">Default Value</span></span> | <span data-ttu-id="ae3d2-126">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ae3d2-127">30 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-127">30 seconds</span></span> | <span data-ttu-id="ae3d2-128">Сервер будет считать клиента отключенным, если он не получил сообщение (включая сохранение жизни) в этом интервале.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ae3d2-129">Это может занять больше времени, чем этот интервал тайм-аута для клиента на самом деле быть помечены отключены, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ae3d2-130">Рекомендуемое значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ae3d2-131">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-131">15 seconds</span></span> | <span data-ttu-id="ae3d2-132">Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ae3d2-133">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-134">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ae3d2-135">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-135">15 seconds</span></span> | <span data-ttu-id="ae3d2-136">Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ae3d2-137">При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ae3d2-138">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ae3d2-139">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="ae3d2-139">All installed protocols</span></span> | <span data-ttu-id="ae3d2-140">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-140">Protocols supported by this hub.</span></span> <span data-ttu-id="ae3d2-141">По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ae3d2-142">Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ae3d2-143">По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="ae3d2-144">Максимальное количество элементов, которые могут быть буферизированы для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="ae3d2-145">Если этот предел достигнут, обработка вызовов блокируется до тех пор, пока сервер не обрабатывает элементы потока.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="ae3d2-146">32 КБ</span><span class="sxs-lookup"><span data-stu-id="ae3d2-146">32 KB</span></span> | <span data-ttu-id="ae3d2-147">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="ae3d2-148">Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .</span><span class="sxs-lookup"><span data-stu-id="ae3d2-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="ae3d2-149">Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ae3d2-150">Расширенные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="ae3d2-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ae3d2-151">Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ae3d2-152">Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в .</span><span class="sxs-lookup"><span data-stu-id="ae3d2-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="ae3d2-153">В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ae3d2-154">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-154">Option</span></span> | <span data-ttu-id="ae3d2-155">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-155">Default Value</span></span> | <span data-ttu-id="ae3d2-156">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ae3d2-157">32 КБ</span><span class="sxs-lookup"><span data-stu-id="ae3d2-157">32 KB</span></span> | <span data-ttu-id="ae3d2-158">Максимальное количество байтов, полученных от клиента, который буферизирует сервер перед применением бэкпрессии.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="ae3d2-159">Увеличение этого значения позволяет серверу получать большие сообщения быстрее, не применяя бэкпрессу, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ae3d2-160">Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ae3d2-161">Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ae3d2-162">32 КБ</span><span class="sxs-lookup"><span data-stu-id="ae3d2-162">32 KB</span></span> | <span data-ttu-id="ae3d2-163">Максимальное количество байтов, отправленных приложением, которое буферизирует сервер перед наблюдением за бэкпрессионом.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="ae3d2-164">Увеличение этого значения позволяет серверу буферизировать большие сообщения быстрее, не ожидая избыточного давления, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ae3d2-165">Все транспортные перевозки включены.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-165">All Transports are enabled.</span></span> | <span data-ttu-id="ae3d2-166">Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ae3d2-167">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-167">See below.</span></span> | <span data-ttu-id="ae3d2-168">Дополнительные опции, специфичные для транспорта Long Polling.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ae3d2-169">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-169">See below.</span></span> | <span data-ttu-id="ae3d2-170">Дополнительные опции, специфичныдля для транспорта WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ae3d2-171">Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ae3d2-172">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-172">Option</span></span> | <span data-ttu-id="ae3d2-173">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-173">Default Value</span></span> | <span data-ttu-id="ae3d2-174">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ae3d2-175">90 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-175">90 seconds</span></span> | <span data-ttu-id="ae3d2-176">Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ae3d2-177">Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ae3d2-178">Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ae3d2-179">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-179">Option</span></span> | <span data-ttu-id="ae3d2-180">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-180">Default Value</span></span> | <span data-ttu-id="ae3d2-181">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ae3d2-182">5 с</span><span class="sxs-lookup"><span data-stu-id="ae3d2-182">5 seconds</span></span> | <span data-ttu-id="ae3d2-183">После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ae3d2-184">Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ae3d2-185">Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ae3d2-186">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="ae3d2-186">Configure client options</span></span>

<span data-ttu-id="ae3d2-187">Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ae3d2-188">Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ae3d2-189">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="ae3d2-189">Configure logging</span></span>

<span data-ttu-id="ae3d2-190">С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ae3d2-191">Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ae3d2-192">Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ae3d2-193">Для регистрации поставщиков logging необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ae3d2-194">Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ae3d2-195">Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ae3d2-196">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ae3d2-197">В клиенте JavaScript `configureLogging` существует аналогичный метод.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ae3d2-198">Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ae3d2-199">Входы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="ae3d2-200">Вместо `LogLevel` значения можно также предоставить `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="ae3d2-201">Это полезно при настройке регистрации SignalR в средах, где `LogLevel` у вас нет доступа к константам.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="ae3d2-202">В следующей таблице перечислены доступные уровни журнала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-202">The following table lists the available log levels.</span></span> <span data-ttu-id="ae3d2-203">Значение, которое `configureLogging` вы предоставляете для набора **минимального** уровня журнала, который будет зарегистрирован.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="ae3d2-204">Сообщения, зарегистрированные на этом уровне, **или уровни, перечисленные после него в таблице,** будут зарегистрированы.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="ae3d2-205">Строка</span><span class="sxs-lookup"><span data-stu-id="ae3d2-205">String</span></span>                      | <span data-ttu-id="ae3d2-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="ae3d2-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="ae3d2-207">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="ae3d2-208">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="ae3d2-209">Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ae3d2-210">Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ae3d2-211">Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ae3d2-212">Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ae3d2-213">Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ae3d2-214">Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ae3d2-215">Это можно смело игнорировать.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ae3d2-216">Настройка разрешенных перевозок</span><span class="sxs-lookup"><span data-stu-id="ae3d2-216">Configure allowed transports</span></span>

<span data-ttu-id="ae3d2-217">Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ae3d2-218">Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ae3d2-219">Все переносы включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-219">All transports are enabled by default.</span></span>

<span data-ttu-id="ae3d2-220">Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ae3d2-221">В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ae3d2-222">В этой версии веб-сокететов клиентов Java является единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="ae3d2-223">В клиенте Java транспорт выбирается `withTransport` с помощью метода `HttpHubConnectionBuilder`на .</span><span class="sxs-lookup"><span data-stu-id="ae3d2-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="ae3d2-224">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ae3d2-225">Клиент SignalR Java пока не поддерживает резервное копирование транспорта.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ae3d2-226">Настройка аутентификации носителей</span><span class="sxs-lookup"><span data-stu-id="ae3d2-226">Configure bearer authentication</span></span>

<span data-ttu-id="ae3d2-227">Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ae3d2-228">В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ae3d2-229">В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ae3d2-230">В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ae3d2-231">В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ae3d2-232">В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="ae3d2-233">В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ae3d2-234">Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ae3d2-235">С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ae3d2-236">Настройка тайм-аута и опций keep-alive</span><span class="sxs-lookup"><span data-stu-id="ae3d2-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ae3d2-237">Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ae3d2-238">.NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ae3d2-239">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-239">Option</span></span> | <span data-ttu-id="ae3d2-240">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-240">Default value</span></span> | <span data-ttu-id="ae3d2-241">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ae3d2-242">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-243">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-243">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-244">Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ae3d2-245">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-246">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ae3d2-247">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-247">15 seconds</span></span> | <span data-ttu-id="ae3d2-248">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="ae3d2-249">Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ae3d2-250">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-251">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ae3d2-252">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-252">15 seconds</span></span> | <span data-ttu-id="ae3d2-253">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ae3d2-254">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ae3d2-255">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ae3d2-256">В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ae3d2-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ae3d2-258">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-258">Option</span></span> | <span data-ttu-id="ae3d2-259">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-259">Default value</span></span> | <span data-ttu-id="ae3d2-260">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ae3d2-261">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-262">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-262">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-263">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ae3d2-264">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-265">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ae3d2-266">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-267">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ae3d2-268">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ae3d2-269">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ae3d2-270">Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-270">Java</span></span>](#tab/java)

| <span data-ttu-id="ae3d2-271">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-271">Option</span></span> | <span data-ttu-id="ae3d2-272">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-272">Default value</span></span> | <span data-ttu-id="ae3d2-273">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ae3d2-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ae3d2-275">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-276">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-276">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-277">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ae3d2-278">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-279">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ae3d2-280">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-280">15 seconds</span></span> | <span data-ttu-id="ae3d2-281">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="ae3d2-282">Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ae3d2-283">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-284">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ae3d2-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ae3d2-286">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-287">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ae3d2-288">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ae3d2-289">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ae3d2-290">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="ae3d2-290">Configure additional options</span></span>

<span data-ttu-id="ae3d2-291">Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ae3d2-292">.NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ae3d2-293">Опция .NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-293">.NET Option</span></span> |  <span data-ttu-id="ae3d2-294">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-294">Default value</span></span> | <span data-ttu-id="ae3d2-295">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ae3d2-296">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ae3d2-297">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-298">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-299">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ae3d2-300">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-300">Empty</span></span> | <span data-ttu-id="ae3d2-301">Коллекция сертификатов TLS для отправки запросов на аутентификации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ae3d2-302">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-302">Empty</span></span> | <span data-ttu-id="ae3d2-303">Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ae3d2-304">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-304">Empty</span></span> | <span data-ttu-id="ae3d2-305">Учетные данные для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ae3d2-306">5 с</span><span class="sxs-lookup"><span data-stu-id="ae3d2-306">5 seconds</span></span> | <span data-ttu-id="ae3d2-307">WebSockets только.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-307">WebSockets only.</span></span> <span data-ttu-id="ae3d2-308">Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ae3d2-309">Если сервер не признает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ae3d2-310">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-310">Empty</span></span> | <span data-ttu-id="ae3d2-311">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ae3d2-312">Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ae3d2-313">Не используется для подключения WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="ae3d2-314">Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ae3d2-315">Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ae3d2-316">**При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ae3d2-317">Прокси HTTP для использования при отправке запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ae3d2-318">Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ae3d2-319">Это позволяет использовать аутентификацию Windows.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ae3d2-320">Делегат, который может быть использован для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ae3d2-321">Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ae3d2-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ae3d2-323">Опция JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-323">JavaScript Option</span></span> | <span data-ttu-id="ae3d2-324">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-324">Default Value</span></span> | <span data-ttu-id="ae3d2-325">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ae3d2-326">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ae3d2-327">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-328">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-329">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ae3d2-330">Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-330">Java</span></span>](#tab/java)

| <span data-ttu-id="ae3d2-331">Вариант Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-331">Java Option</span></span> | <span data-ttu-id="ae3d2-332">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-332">Default Value</span></span> | <span data-ttu-id="ae3d2-333">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ae3d2-334">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ae3d2-335">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-336">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-337">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ae3d2-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ae3d2-339">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-339">Empty</span></span> | <span data-ttu-id="ae3d2-340">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ae3d2-341">В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ae3d2-342">В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ae3d2-343">В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ae3d2-344">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ae3d2-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ae3d2-345">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ae3d2-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ae3d2-346">ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ae3d2-347">Каждый протокол имеет параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ae3d2-348">Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) который может быть добавлен после [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в вашем `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ae3d2-349">Метод `AddJsonProtocol` принимает делегата, `options` который получает объект.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ae3d2-350">Свойство [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) на этом `JsonSerializerSettings` объекте является JSON.NET объектом, который может быть использован для настройки последовательности аргументов и значений возврата.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ae3d2-351">Для получения дополнительной информации смотрите [JSON.NET документацию](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="ae3d2-352">В качестве примера, чтобы настроить сериализатор, чтобы использовать имена свойств "PascalCase", вместо имен `Startup.ConfigureServices`"camelCase" по умолчанию, используйте следующий код в:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="ae3d2-353">В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ae3d2-354">Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="ae3d2-355">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ae3d2-356">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="ae3d2-356">MessagePack serialization options</span></span>

<span data-ttu-id="ae3d2-357">Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ae3d2-358">Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ae3d2-359">На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ae3d2-360">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="ae3d2-360">Configure server options</span></span>

<span data-ttu-id="ae3d2-361">В следующей таблице описаны варианты настройки концентраторов SignalR:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ae3d2-362">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-362">Option</span></span> | <span data-ttu-id="ae3d2-363">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-363">Default Value</span></span> | <span data-ttu-id="ae3d2-364">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ae3d2-365">30 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-365">30 seconds</span></span> | <span data-ttu-id="ae3d2-366">Сервер будет считать клиента отключенным, если он не получил сообщение (включая сохранение жизни) в этом интервале.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ae3d2-367">Это может занять больше времени, чем этот интервал тайм-аута для клиента на самом деле быть помечены отключены, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ae3d2-368">Рекомендуемое значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ae3d2-369">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-369">15 seconds</span></span> | <span data-ttu-id="ae3d2-370">Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ae3d2-371">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-372">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ae3d2-373">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-373">15 seconds</span></span> | <span data-ttu-id="ae3d2-374">Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ae3d2-375">При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ae3d2-376">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ae3d2-377">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="ae3d2-377">All installed protocols</span></span> | <span data-ttu-id="ae3d2-378">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-378">Protocols supported by this hub.</span></span> <span data-ttu-id="ae3d2-379">По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ae3d2-380">Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ae3d2-381">По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="ae3d2-382">Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .</span><span class="sxs-lookup"><span data-stu-id="ae3d2-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="ae3d2-383">Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ae3d2-384">Расширенные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="ae3d2-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ae3d2-385">Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ae3d2-386">Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в .</span><span class="sxs-lookup"><span data-stu-id="ae3d2-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="ae3d2-387">В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ae3d2-388">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-388">Option</span></span> | <span data-ttu-id="ae3d2-389">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-389">Default Value</span></span> | <span data-ttu-id="ae3d2-390">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ae3d2-391">32 КБ</span><span class="sxs-lookup"><span data-stu-id="ae3d2-391">32 KB</span></span> | <span data-ttu-id="ae3d2-392">Максимальное количество байтов, полученных от клиента, буфера сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="ae3d2-393">Увеличение этого значения позволяет серверу получать большие сообщения, но может негативно повлиять на потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ae3d2-394">Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ae3d2-395">Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ae3d2-396">32 КБ</span><span class="sxs-lookup"><span data-stu-id="ae3d2-396">32 KB</span></span> | <span data-ttu-id="ae3d2-397">Максимальное количество байтов, отправленных приложением, которое буферизирует сервер.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="ae3d2-398">Увеличение этого значения позволяет серверу отправлять большие сообщения, но может негативно повлиять на потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ae3d2-399">Все транспортные перевозки включены.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-399">All Transports are enabled.</span></span> | <span data-ttu-id="ae3d2-400">Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ae3d2-401">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-401">See below.</span></span> | <span data-ttu-id="ae3d2-402">Дополнительные опции, специфичные для транспорта Long Polling.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ae3d2-403">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-403">See below.</span></span> | <span data-ttu-id="ae3d2-404">Дополнительные опции, специфичныдля для транспорта WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ae3d2-405">Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ae3d2-406">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-406">Option</span></span> | <span data-ttu-id="ae3d2-407">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-407">Default Value</span></span> | <span data-ttu-id="ae3d2-408">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ae3d2-409">90 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-409">90 seconds</span></span> | <span data-ttu-id="ae3d2-410">Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ae3d2-411">Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ae3d2-412">Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ae3d2-413">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-413">Option</span></span> | <span data-ttu-id="ae3d2-414">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-414">Default Value</span></span> | <span data-ttu-id="ae3d2-415">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ae3d2-416">5 с</span><span class="sxs-lookup"><span data-stu-id="ae3d2-416">5 seconds</span></span> | <span data-ttu-id="ae3d2-417">После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ae3d2-418">Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ae3d2-419">Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ae3d2-420">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="ae3d2-420">Configure client options</span></span>

<span data-ttu-id="ae3d2-421">Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ae3d2-422">Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ae3d2-423">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="ae3d2-423">Configure logging</span></span>

<span data-ttu-id="ae3d2-424">С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ae3d2-425">Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ae3d2-426">Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ae3d2-427">Для регистрации поставщиков logging необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ae3d2-428">Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ae3d2-429">Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ae3d2-430">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ae3d2-431">В клиенте JavaScript `configureLogging` существует аналогичный метод.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ae3d2-432">Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ae3d2-433">Входы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ae3d2-434">Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ae3d2-435">Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ae3d2-436">Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ae3d2-437">Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ae3d2-438">Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ae3d2-439">Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ae3d2-440">Это можно смело игнорировать.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ae3d2-441">Настройка разрешенных перевозок</span><span class="sxs-lookup"><span data-stu-id="ae3d2-441">Configure allowed transports</span></span>

<span data-ttu-id="ae3d2-442">Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ae3d2-443">Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ae3d2-444">Все переносы включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-444">All transports are enabled by default.</span></span>

<span data-ttu-id="ae3d2-445">Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ae3d2-446">В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ae3d2-447">В этой версии веб-сокететов клиентов Java является единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ae3d2-448">Настройка аутентификации носителей</span><span class="sxs-lookup"><span data-stu-id="ae3d2-448">Configure bearer authentication</span></span>

<span data-ttu-id="ae3d2-449">Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ae3d2-450">В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ae3d2-451">В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ae3d2-452">В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ae3d2-453">В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ae3d2-454">В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="ae3d2-455">В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ae3d2-456">Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ae3d2-457">С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ae3d2-458">Настройка тайм-аута и опций keep-alive</span><span class="sxs-lookup"><span data-stu-id="ae3d2-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ae3d2-459">Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ae3d2-460">.NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ae3d2-461">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-461">Option</span></span> | <span data-ttu-id="ae3d2-462">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-462">Default value</span></span> | <span data-ttu-id="ae3d2-463">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ae3d2-464">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-465">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-465">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-466">Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ae3d2-467">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-468">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ae3d2-469">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-469">15 seconds</span></span> | <span data-ttu-id="ae3d2-470">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="ae3d2-471">Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ae3d2-472">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-473">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ae3d2-474">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-474">15 seconds</span></span> | <span data-ttu-id="ae3d2-475">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ae3d2-476">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ae3d2-477">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ae3d2-478">В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ae3d2-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ae3d2-480">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-480">Option</span></span> | <span data-ttu-id="ae3d2-481">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-481">Default value</span></span> | <span data-ttu-id="ae3d2-482">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ae3d2-483">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-484">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-484">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-485">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ae3d2-486">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-487">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ae3d2-488">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-489">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ae3d2-490">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ae3d2-491">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ae3d2-492">Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-492">Java</span></span>](#tab/java)

| <span data-ttu-id="ae3d2-493">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-493">Option</span></span> | <span data-ttu-id="ae3d2-494">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-494">Default value</span></span> | <span data-ttu-id="ae3d2-495">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ae3d2-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ae3d2-497">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-498">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-498">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-499">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ae3d2-500">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-501">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ae3d2-502">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-502">15 seconds</span></span> | <span data-ttu-id="ae3d2-503">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="ae3d2-504">Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ae3d2-505">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-506">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ae3d2-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ae3d2-508">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-509">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ae3d2-510">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ae3d2-511">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ae3d2-512">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="ae3d2-512">Configure additional options</span></span>

<span data-ttu-id="ae3d2-513">Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ae3d2-514">.NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ae3d2-515">Опция .NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-515">.NET Option</span></span> |  <span data-ttu-id="ae3d2-516">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-516">Default value</span></span> | <span data-ttu-id="ae3d2-517">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ae3d2-518">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ae3d2-519">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-520">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-521">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ae3d2-522">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-522">Empty</span></span> | <span data-ttu-id="ae3d2-523">Коллекция сертификатов TLS для отправки запросов на аутентификации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ae3d2-524">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-524">Empty</span></span> | <span data-ttu-id="ae3d2-525">Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ae3d2-526">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-526">Empty</span></span> | <span data-ttu-id="ae3d2-527">Учетные данные для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ae3d2-528">5 с</span><span class="sxs-lookup"><span data-stu-id="ae3d2-528">5 seconds</span></span> | <span data-ttu-id="ae3d2-529">WebSockets только.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-529">WebSockets only.</span></span> <span data-ttu-id="ae3d2-530">Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ae3d2-531">Если сервер не признает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ae3d2-532">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-532">Empty</span></span> | <span data-ttu-id="ae3d2-533">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ae3d2-534">Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ae3d2-535">Не используется для подключения WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="ae3d2-536">Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ae3d2-537">Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ae3d2-538">**При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ae3d2-539">Прокси HTTP для использования при отправке запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ae3d2-540">Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ae3d2-541">Это позволяет использовать аутентификацию Windows.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ae3d2-542">Делегат, который может быть использован для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ae3d2-543">Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ae3d2-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ae3d2-545">Опция JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-545">JavaScript Option</span></span> | <span data-ttu-id="ae3d2-546">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-546">Default Value</span></span> | <span data-ttu-id="ae3d2-547">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ae3d2-548">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ae3d2-549">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-550">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-551">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ae3d2-552">Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-552">Java</span></span>](#tab/java)

| <span data-ttu-id="ae3d2-553">Вариант Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-553">Java Option</span></span> | <span data-ttu-id="ae3d2-554">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-554">Default Value</span></span> | <span data-ttu-id="ae3d2-555">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ae3d2-556">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ae3d2-557">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-558">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-559">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ae3d2-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ae3d2-561">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-561">Empty</span></span> | <span data-ttu-id="ae3d2-562">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ae3d2-563">В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ae3d2-564">В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ae3d2-565">В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ae3d2-566">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ae3d2-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ae3d2-567">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ae3d2-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ae3d2-568">ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ae3d2-569">Каждый протокол имеет параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ae3d2-570">Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) который может быть добавлен после [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в вашем `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ae3d2-571">Метод `AddJsonProtocol` принимает делегата, `options` который получает объект.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ae3d2-572">Свойство [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) на этом `JsonSerializerSettings` объекте является JSON.NET объектом, который может быть использован для настройки последовательности аргументов и значений возврата.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ae3d2-573">Для получения дополнительной информации смотрите [JSON.NET документацию](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="ae3d2-574">В качестве примера, чтобы настроить сериализатор, чтобы использовать имена свойств "PascalCase", вместо имен `Startup.ConfigureServices`"camelCase" по умолчанию, используйте следующий код в:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="ae3d2-575">В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ae3d2-576">Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="ae3d2-577">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ae3d2-578">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="ae3d2-578">MessagePack serialization options</span></span>

<span data-ttu-id="ae3d2-579">Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ae3d2-580">Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ae3d2-581">На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ae3d2-582">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="ae3d2-582">Configure server options</span></span>

<span data-ttu-id="ae3d2-583">В следующей таблице описаны варианты настройки концентраторов SignalR:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ae3d2-584">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-584">Option</span></span> | <span data-ttu-id="ae3d2-585">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-585">Default Value</span></span> | <span data-ttu-id="ae3d2-586">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="ae3d2-587">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-587">15 seconds</span></span> | <span data-ttu-id="ae3d2-588">Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ae3d2-589">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-590">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ae3d2-591">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-591">15 seconds</span></span> | <span data-ttu-id="ae3d2-592">Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ae3d2-593">При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ae3d2-594">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ae3d2-595">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="ae3d2-595">All installed protocols</span></span> | <span data-ttu-id="ae3d2-596">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-596">Protocols supported by this hub.</span></span> <span data-ttu-id="ae3d2-597">По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ae3d2-598">Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ae3d2-599">По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="ae3d2-600">Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .</span><span class="sxs-lookup"><span data-stu-id="ae3d2-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="ae3d2-601">Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ae3d2-602">Расширенные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="ae3d2-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ae3d2-603">Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ae3d2-604">Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в .</span><span class="sxs-lookup"><span data-stu-id="ae3d2-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="ae3d2-605">В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ae3d2-606">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-606">Option</span></span> | <span data-ttu-id="ae3d2-607">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-607">Default Value</span></span> | <span data-ttu-id="ae3d2-608">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ae3d2-609">32 КБ</span><span class="sxs-lookup"><span data-stu-id="ae3d2-609">32 KB</span></span> | <span data-ttu-id="ae3d2-610">Максимальное количество байтов, полученных от клиента, буфера сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="ae3d2-611">Увеличение этого значения позволяет серверу получать большие сообщения, но может негативно повлиять на потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ae3d2-612">Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ae3d2-613">Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ae3d2-614">32 КБ</span><span class="sxs-lookup"><span data-stu-id="ae3d2-614">32 KB</span></span> | <span data-ttu-id="ae3d2-615">Максимальное количество байтов, отправленных приложением, которое буферизирует сервер.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="ae3d2-616">Увеличение этого значения позволяет серверу отправлять большие сообщения, но может негативно повлиять на потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ae3d2-617">Все транспортные перевозки включены.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-617">All Transports are enabled.</span></span> | <span data-ttu-id="ae3d2-618">Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ae3d2-619">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-619">See below.</span></span> | <span data-ttu-id="ae3d2-620">Дополнительные опции, специфичные для транспорта Long Polling.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ae3d2-621">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-621">See below.</span></span> | <span data-ttu-id="ae3d2-622">Дополнительные опции, специфичныдля для транспорта WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ae3d2-623">Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ae3d2-624">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-624">Option</span></span> | <span data-ttu-id="ae3d2-625">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-625">Default Value</span></span> | <span data-ttu-id="ae3d2-626">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ae3d2-627">90 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-627">90 seconds</span></span> | <span data-ttu-id="ae3d2-628">Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ae3d2-629">Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ae3d2-630">Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ae3d2-631">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-631">Option</span></span> | <span data-ttu-id="ae3d2-632">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-632">Default Value</span></span> | <span data-ttu-id="ae3d2-633">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ae3d2-634">5 с</span><span class="sxs-lookup"><span data-stu-id="ae3d2-634">5 seconds</span></span> | <span data-ttu-id="ae3d2-635">После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ae3d2-636">Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ae3d2-637">Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ae3d2-638">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="ae3d2-638">Configure client options</span></span>

<span data-ttu-id="ae3d2-639">Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ae3d2-640">Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ae3d2-641">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="ae3d2-641">Configure logging</span></span>

<span data-ttu-id="ae3d2-642">С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ae3d2-643">Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ae3d2-644">Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ae3d2-645">Для регистрации поставщиков logging необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ae3d2-646">Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ae3d2-647">Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ae3d2-648">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ae3d2-649">В клиенте JavaScript `configureLogging` существует аналогичный метод.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ae3d2-650">Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ae3d2-651">Входы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ae3d2-652">Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ae3d2-653">Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ae3d2-654">Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ae3d2-655">Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ae3d2-656">Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ae3d2-657">Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ae3d2-658">Это можно смело игнорировать.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ae3d2-659">Настройка разрешенных перевозок</span><span class="sxs-lookup"><span data-stu-id="ae3d2-659">Configure allowed transports</span></span>

<span data-ttu-id="ae3d2-660">Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ae3d2-661">Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ae3d2-662">Все переносы включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-662">All transports are enabled by default.</span></span>

<span data-ttu-id="ae3d2-663">Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ae3d2-664">В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ae3d2-665">Настройка аутентификации носителей</span><span class="sxs-lookup"><span data-stu-id="ae3d2-665">Configure bearer authentication</span></span>

<span data-ttu-id="ae3d2-666">Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ae3d2-667">В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ae3d2-668">В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ae3d2-669">В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ae3d2-670">В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ae3d2-671">В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="ae3d2-672">В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ae3d2-673">Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ae3d2-674">С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ae3d2-675">Настройка тайм-аута и опций keep-alive</span><span class="sxs-lookup"><span data-stu-id="ae3d2-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ae3d2-676">Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ae3d2-677">.NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ae3d2-678">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-678">Option</span></span> | <span data-ttu-id="ae3d2-679">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-679">Default value</span></span> | <span data-ttu-id="ae3d2-680">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ae3d2-681">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-682">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-682">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-683">Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ae3d2-684">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-685">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ae3d2-686">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-686">15 seconds</span></span> | <span data-ttu-id="ae3d2-687">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="ae3d2-688">Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ae3d2-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ae3d2-689">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-690">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="ae3d2-691">В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ae3d2-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ae3d2-693">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-693">Option</span></span> | <span data-ttu-id="ae3d2-694">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-694">Default value</span></span> | <span data-ttu-id="ae3d2-695">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ae3d2-696">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-697">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-697">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-698">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ae3d2-699">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-700">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ae3d2-701">Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-701">Java</span></span>](#tab/java)

| <span data-ttu-id="ae3d2-702">Параметр</span><span class="sxs-lookup"><span data-stu-id="ae3d2-702">Option</span></span> | <span data-ttu-id="ae3d2-703">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-703">Default value</span></span> | <span data-ttu-id="ae3d2-704">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ae3d2-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ae3d2-706">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="ae3d2-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ae3d2-707">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-707">Timeout for server activity.</span></span> <span data-ttu-id="ae3d2-708">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ae3d2-709">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ae3d2-710">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше значения сервера, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ae3d2-711">15 секунд</span><span class="sxs-lookup"><span data-stu-id="ae3d2-711">15 seconds</span></span> | <span data-ttu-id="ae3d2-712">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="ae3d2-713">Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ae3d2-714">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ae3d2-715">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ae3d2-716">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="ae3d2-716">Configure additional options</span></span>

<span data-ttu-id="ae3d2-717">Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ae3d2-718">.NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ae3d2-719">Опция .NET</span><span class="sxs-lookup"><span data-stu-id="ae3d2-719">.NET Option</span></span> |  <span data-ttu-id="ae3d2-720">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-720">Default value</span></span> | <span data-ttu-id="ae3d2-721">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ae3d2-722">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ae3d2-723">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-724">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-725">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ae3d2-726">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-726">Empty</span></span> | <span data-ttu-id="ae3d2-727">Коллекция сертификатов TLS для отправки запросов на аутентификации.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ae3d2-728">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-728">Empty</span></span> | <span data-ttu-id="ae3d2-729">Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ae3d2-730">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-730">Empty</span></span> | <span data-ttu-id="ae3d2-731">Учетные данные для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ae3d2-732">5 с</span><span class="sxs-lookup"><span data-stu-id="ae3d2-732">5 seconds</span></span> | <span data-ttu-id="ae3d2-733">WebSockets только.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-733">WebSockets only.</span></span> <span data-ttu-id="ae3d2-734">Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ae3d2-735">Если сервер не признает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ae3d2-736">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-736">Empty</span></span> | <span data-ttu-id="ae3d2-737">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ae3d2-738">Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ae3d2-739">Не используется для подключения WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="ae3d2-740">Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ae3d2-741">Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ae3d2-742">**При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ae3d2-743">Прокси HTTP для использования при отправке запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ae3d2-744">Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ae3d2-745">Это позволяет использовать аутентификацию Windows.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ae3d2-746">Делегат, который может быть использован для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ae3d2-747">Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ae3d2-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ae3d2-749">Опция JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae3d2-749">JavaScript Option</span></span> | <span data-ttu-id="ae3d2-750">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-750">Default Value</span></span> | <span data-ttu-id="ae3d2-751">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ae3d2-752">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ae3d2-753">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-754">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-755">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ae3d2-756">Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-756">Java</span></span>](#tab/java)

| <span data-ttu-id="ae3d2-757">Вариант Java</span><span class="sxs-lookup"><span data-stu-id="ae3d2-757">Java Option</span></span> | <span data-ttu-id="ae3d2-758">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae3d2-758">Default Value</span></span> | <span data-ttu-id="ae3d2-759">Описание</span><span class="sxs-lookup"><span data-stu-id="ae3d2-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ae3d2-760">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ae3d2-761">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ae3d2-762">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="ae3d2-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ae3d2-763">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ae3d2-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ae3d2-765">Empty</span><span class="sxs-lookup"><span data-stu-id="ae3d2-765">Empty</span></span> | <span data-ttu-id="ae3d2-766">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae3d2-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ae3d2-767">В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ae3d2-768">В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="ae3d2-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ae3d2-769">В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ae3d2-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ae3d2-770">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ae3d2-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
