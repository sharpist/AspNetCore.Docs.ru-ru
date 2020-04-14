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
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228144"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="5568a-103">Конфигурация SignalR для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5568a-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5568a-104">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5568a-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5568a-105">ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="5568a-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5568a-106">Каждый протокол имеет параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="5568a-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5568a-107">Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="5568a-108">`AddJsonProtocol`могут быть добавлены после `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в .</span><span class="sxs-lookup"><span data-stu-id="5568a-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5568a-109">Метод `AddJsonProtocol` принимает делегата, `options` который получает объект.</span><span class="sxs-lookup"><span data-stu-id="5568a-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5568a-110">Свойство [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) на этом `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объекте является объектом, который может быть использован для настройки последовательности аргументов и значений возврата.</span><span class="sxs-lookup"><span data-stu-id="5568a-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5568a-111">Для получения дополнительной [информации](/dotnet/api/system.text.json)см.</span><span class="sxs-lookup"><span data-stu-id="5568a-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="5568a-112">В качестве примера, чтобы настроить сериалайзер, чтобы не изменять корпус имен свойств, вместо имен "camelCase" по умолчанию, используйте следующий код в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="5568a-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="5568a-113">В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="5568a-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5568a-114">Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:</span><span class="sxs-lookup"><span data-stu-id="5568a-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5568a-115">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5568a-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="5568a-116">Переключиться на Ньютонсофт.Джсон</span><span class="sxs-lookup"><span data-stu-id="5568a-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="5568a-117">Если вам нужны `Newtonsoft.Json` функции, которые `System.Text.Json`не поддерживаются в , см. [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)</span><span class="sxs-lookup"><span data-stu-id="5568a-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5568a-118">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="5568a-118">MessagePack serialization options</span></span>

<span data-ttu-id="5568a-119">Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5568a-120">Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5568a-121">На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5568a-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5568a-122">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="5568a-122">Configure server options</span></span>

<span data-ttu-id="5568a-123">В следующей таблице описаны варианты настройки концентраторов SignalR:</span><span class="sxs-lookup"><span data-stu-id="5568a-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5568a-124">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-124">Option</span></span> | <span data-ttu-id="5568a-125">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-125">Default Value</span></span> | <span data-ttu-id="5568a-126">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5568a-127">30 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-127">30 seconds</span></span> | <span data-ttu-id="5568a-128">Сервер будет считать клиента отключенным, если он не получил сообщение (включая сохранение жизни) в этом интервале.</span><span class="sxs-lookup"><span data-stu-id="5568a-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5568a-129">Это может занять больше времени, чем этот интервал тайм-аута для клиента на самом деле быть помечены отключены, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="5568a-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5568a-130">Рекомендуемое значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5568a-131">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-131">15 seconds</span></span> | <span data-ttu-id="5568a-132">Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="5568a-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5568a-133">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-134">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5568a-135">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-135">15 seconds</span></span> | <span data-ttu-id="5568a-136">Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="5568a-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5568a-137">При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5568a-138">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5568a-139">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="5568a-139">All installed protocols</span></span> | <span data-ttu-id="5568a-140">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="5568a-140">Protocols supported by this hub.</span></span> <span data-ttu-id="5568a-141">По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="5568a-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5568a-142">Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5568a-143">По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="5568a-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="5568a-144">Максимальное количество элементов, которые могут быть буферизированы для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="5568a-145">Если этот предел достигнут, обработка вызовов блокируется до тех пор, пока сервер не обрабатывает элементы потока.</span><span class="sxs-lookup"><span data-stu-id="5568a-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="5568a-146">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-146">32 KB</span></span> | <span data-ttu-id="5568a-147">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="5568a-148">Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .</span><span class="sxs-lookup"><span data-stu-id="5568a-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5568a-149">Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:</span><span class="sxs-lookup"><span data-stu-id="5568a-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5568a-150">Расширенные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="5568a-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5568a-151">Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5568a-152">Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в .</span><span class="sxs-lookup"><span data-stu-id="5568a-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="5568a-153">В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="5568a-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5568a-154">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-154">Option</span></span> | <span data-ttu-id="5568a-155">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-155">Default Value</span></span> | <span data-ttu-id="5568a-156">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5568a-157">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-157">32 KB</span></span> | <span data-ttu-id="5568a-158">Максимальное количество байтов, полученных от клиента, который буферизирует сервер перед применением бэкпрессии.</span><span class="sxs-lookup"><span data-stu-id="5568a-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="5568a-159">Увеличение этого значения позволяет серверу получать большие сообщения быстрее, не применяя бэкпрессу, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5568a-160">Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5568a-161">Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору.</span><span class="sxs-lookup"><span data-stu-id="5568a-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5568a-162">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-162">32 KB</span></span> | <span data-ttu-id="5568a-163">Максимальное количество байтов, отправленных приложением, которое буферизирует сервер перед наблюдением за бэкпрессионом.</span><span class="sxs-lookup"><span data-stu-id="5568a-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="5568a-164">Увеличение этого значения позволяет серверу буферизировать большие сообщения быстрее, не ожидая избыточного давления, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5568a-165">Все транспортные перевозки включены.</span><span class="sxs-lookup"><span data-stu-id="5568a-165">All Transports are enabled.</span></span> | <span data-ttu-id="5568a-166">Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="5568a-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5568a-167">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="5568a-167">See below.</span></span> | <span data-ttu-id="5568a-168">Дополнительные опции, специфичные для транспорта Long Polling.</span><span class="sxs-lookup"><span data-stu-id="5568a-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5568a-169">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="5568a-169">See below.</span></span> | <span data-ttu-id="5568a-170">Дополнительные опции, специфичныдля для транспорта WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="5568a-171">0</span><span class="sxs-lookup"><span data-stu-id="5568a-171">0</span></span> | <span data-ttu-id="5568a-172">Укажите минимальную версию протокола переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="5568a-173">Это используется для ограничения клиентов новыми версиями.</span><span class="sxs-lookup"><span data-stu-id="5568a-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="5568a-174">Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="5568a-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5568a-175">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-175">Option</span></span> | <span data-ttu-id="5568a-176">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-176">Default Value</span></span> | <span data-ttu-id="5568a-177">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5568a-178">90 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-178">90 seconds</span></span> | <span data-ttu-id="5568a-179">Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5568a-180">Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5568a-181">Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="5568a-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5568a-182">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-182">Option</span></span> | <span data-ttu-id="5568a-183">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-183">Default Value</span></span> | <span data-ttu-id="5568a-184">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5568a-185">5 с</span><span class="sxs-lookup"><span data-stu-id="5568a-185">5 seconds</span></span> | <span data-ttu-id="5568a-186">После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается.</span><span class="sxs-lookup"><span data-stu-id="5568a-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5568a-187">Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5568a-188">Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5568a-189">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="5568a-189">Configure client options</span></span>

<span data-ttu-id="5568a-190">Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5568a-191">Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.</span><span class="sxs-lookup"><span data-stu-id="5568a-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5568a-192">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="5568a-192">Configure logging</span></span>

<span data-ttu-id="5568a-193">С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование.</span><span class="sxs-lookup"><span data-stu-id="5568a-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5568a-194">Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="5568a-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5568a-195">Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.</span><span class="sxs-lookup"><span data-stu-id="5568a-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5568a-196">Для регистрации поставщиков logging необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="5568a-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5568a-197">Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.</span><span class="sxs-lookup"><span data-stu-id="5568a-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5568a-198">Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="5568a-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5568a-199">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="5568a-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5568a-200">В клиенте JavaScript `configureLogging` существует аналогичный метод.</span><span class="sxs-lookup"><span data-stu-id="5568a-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5568a-201">Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения.</span><span class="sxs-lookup"><span data-stu-id="5568a-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5568a-202">Входы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="5568a-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="5568a-203">Вместо `LogLevel` значения можно также предоставить `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="5568a-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="5568a-204">Это полезно при настройке регистрации SignalR в средах, где `LogLevel` у вас нет доступа к константам.</span><span class="sxs-lookup"><span data-stu-id="5568a-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="5568a-205">В следующей таблице перечислены доступные уровни журнала.</span><span class="sxs-lookup"><span data-stu-id="5568a-205">The following table lists the available log levels.</span></span> <span data-ttu-id="5568a-206">Значение, которое `configureLogging` вы предоставляете для набора **минимального** уровня журнала, который будет зарегистрирован.</span><span class="sxs-lookup"><span data-stu-id="5568a-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="5568a-207">Сообщения, зарегистрированные на этом уровне, **или уровни, перечисленные после него в таблице,** будут зарегистрированы.</span><span class="sxs-lookup"><span data-stu-id="5568a-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="5568a-208">Строка</span><span class="sxs-lookup"><span data-stu-id="5568a-208">String</span></span>                      | <span data-ttu-id="5568a-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="5568a-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="5568a-210">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="5568a-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="5568a-211">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="5568a-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="5568a-212">Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="5568a-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5568a-213">Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.</span><span class="sxs-lookup"><span data-stu-id="5568a-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5568a-214">Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="5568a-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5568a-215">Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации.</span><span class="sxs-lookup"><span data-stu-id="5568a-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5568a-216">Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="5568a-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5568a-217">Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="5568a-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5568a-218">Это можно смело игнорировать.</span><span class="sxs-lookup"><span data-stu-id="5568a-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5568a-219">Настройка разрешенных перевозок</span><span class="sxs-lookup"><span data-stu-id="5568a-219">Configure allowed transports</span></span>

<span data-ttu-id="5568a-220">Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5568a-221">Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки.</span><span class="sxs-lookup"><span data-stu-id="5568a-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5568a-222">Все переносы включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5568a-222">All transports are enabled by default.</span></span>

<span data-ttu-id="5568a-223">Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:</span><span class="sxs-lookup"><span data-stu-id="5568a-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5568a-224">В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="5568a-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="5568a-225">В этой версии веб-сокететов клиентов Java является единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="5568a-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="5568a-226">В клиенте Java транспорт выбирается `withTransport` с помощью метода `HttpHubConnectionBuilder`на .</span><span class="sxs-lookup"><span data-stu-id="5568a-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="5568a-227">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5568a-228">Клиент SignalR Java пока не поддерживает резервное копирование транспорта.</span><span class="sxs-lookup"><span data-stu-id="5568a-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5568a-229">Настройка аутентификации носителей</span><span class="sxs-lookup"><span data-stu-id="5568a-229">Configure bearer authentication</span></span>

<span data-ttu-id="5568a-230">Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа.</span><span class="sxs-lookup"><span data-stu-id="5568a-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5568a-231">В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом).</span><span class="sxs-lookup"><span data-stu-id="5568a-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5568a-232">В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5568a-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5568a-233">В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5568a-234">В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="5568a-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5568a-235">В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:</span><span class="sxs-lookup"><span data-stu-id="5568a-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="5568a-236">В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="5568a-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5568a-237">Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="5568a-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5568a-238">С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5568a-239">Настройка тайм-аута и опций keep-alive</span><span class="sxs-lookup"><span data-stu-id="5568a-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5568a-240">Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:</span><span class="sxs-lookup"><span data-stu-id="5568a-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5568a-241">.NET</span><span class="sxs-lookup"><span data-stu-id="5568a-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5568a-242">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-242">Option</span></span> | <span data-ttu-id="5568a-243">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-243">Default value</span></span> | <span data-ttu-id="5568a-244">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5568a-245">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-246">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-246">Timeout for server activity.</span></span> <span data-ttu-id="5568a-247">Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5568a-248">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-249">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5568a-250">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-250">15 seconds</span></span> | <span data-ttu-id="5568a-251">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="5568a-252">Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5568a-253">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-254">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5568a-255">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-255">15 seconds</span></span> | <span data-ttu-id="5568a-256">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-257">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-258">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="5568a-259">В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.</span><span class="sxs-lookup"><span data-stu-id="5568a-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5568a-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5568a-261">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-261">Option</span></span> | <span data-ttu-id="5568a-262">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-262">Default value</span></span> | <span data-ttu-id="5568a-263">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5568a-264">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-265">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-265">Timeout for server activity.</span></span> <span data-ttu-id="5568a-266">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5568a-267">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-268">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="5568a-269">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5568a-270">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-271">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-272">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5568a-273">Java</span><span class="sxs-lookup"><span data-stu-id="5568a-273">Java</span></span>](#tab/java)

| <span data-ttu-id="5568a-274">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-274">Option</span></span> | <span data-ttu-id="5568a-275">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-275">Default value</span></span> | <span data-ttu-id="5568a-276">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5568a-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5568a-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5568a-278">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-279">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-279">Timeout for server activity.</span></span> <span data-ttu-id="5568a-280">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5568a-281">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-282">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5568a-283">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-283">15 seconds</span></span> | <span data-ttu-id="5568a-284">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="5568a-285">Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5568a-286">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-287">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="5568a-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="5568a-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="5568a-289">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5568a-290">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-291">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-292">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5568a-293">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="5568a-293">Configure additional options</span></span>

<span data-ttu-id="5568a-294">Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="5568a-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5568a-295">.NET</span><span class="sxs-lookup"><span data-stu-id="5568a-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5568a-296">Опция .NET</span><span class="sxs-lookup"><span data-stu-id="5568a-296">.NET Option</span></span> |  <span data-ttu-id="5568a-297">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-297">Default value</span></span> | <span data-ttu-id="5568a-298">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5568a-299">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5568a-300">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-301">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-302">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5568a-303">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-303">Empty</span></span> | <span data-ttu-id="5568a-304">Коллекция сертификатов TLS для отправки запросов на аутентификации.</span><span class="sxs-lookup"><span data-stu-id="5568a-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5568a-305">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-305">Empty</span></span> | <span data-ttu-id="5568a-306">Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5568a-307">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-307">Empty</span></span> | <span data-ttu-id="5568a-308">Учетные данные для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5568a-309">5 с</span><span class="sxs-lookup"><span data-stu-id="5568a-309">5 seconds</span></span> | <span data-ttu-id="5568a-310">WebSockets только.</span><span class="sxs-lookup"><span data-stu-id="5568a-310">WebSockets only.</span></span> <span data-ttu-id="5568a-311">Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5568a-312">Если сервер не признает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="5568a-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5568a-313">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-313">Empty</span></span> | <span data-ttu-id="5568a-314">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5568a-315">Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5568a-316">Не используется для подключения WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5568a-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="5568a-317">Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="5568a-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5568a-318">Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="5568a-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5568a-319">**При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="5568a-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5568a-320">Прокси HTTP для использования при отправке запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5568a-321">Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5568a-322">Это позволяет использовать аутентификацию Windows.</span><span class="sxs-lookup"><span data-stu-id="5568a-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5568a-323">Делегат, который может быть использован для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5568a-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5568a-324">Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="5568a-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5568a-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5568a-326">Опция JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-326">JavaScript Option</span></span> | <span data-ttu-id="5568a-327">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-327">Default Value</span></span> | <span data-ttu-id="5568a-328">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5568a-329">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5568a-330">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-331">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-332">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5568a-333">Java</span><span class="sxs-lookup"><span data-stu-id="5568a-333">Java</span></span>](#tab/java)

| <span data-ttu-id="5568a-334">Вариант Java</span><span class="sxs-lookup"><span data-stu-id="5568a-334">Java Option</span></span> | <span data-ttu-id="5568a-335">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-335">Default Value</span></span> | <span data-ttu-id="5568a-336">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5568a-337">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5568a-338">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-339">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-340">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5568a-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5568a-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5568a-342">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-342">Empty</span></span> | <span data-ttu-id="5568a-343">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5568a-344">В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:</span><span class="sxs-lookup"><span data-stu-id="5568a-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5568a-345">В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="5568a-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5568a-346">В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5568a-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5568a-347">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5568a-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5568a-348">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5568a-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5568a-349">ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="5568a-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5568a-350">Каждый протокол имеет параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="5568a-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5568a-351">Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="5568a-352">`AddJsonProtocol`могут быть добавлены после `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в .</span><span class="sxs-lookup"><span data-stu-id="5568a-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5568a-353">Метод `AddJsonProtocol` принимает делегата, `options` который получает объект.</span><span class="sxs-lookup"><span data-stu-id="5568a-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5568a-354">Свойство [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) на этом `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объекте является объектом, который может быть использован для настройки последовательности аргументов и значений возврата.</span><span class="sxs-lookup"><span data-stu-id="5568a-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5568a-355">Для получения дополнительной [информации](/dotnet/api/system.text.json)см.</span><span class="sxs-lookup"><span data-stu-id="5568a-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="5568a-356">В качестве примера, чтобы настроить сериалайзер, чтобы не изменять корпус имен свойств, вместо имен "camelCase" по умолчанию, используйте следующий код в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="5568a-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="5568a-357">В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="5568a-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5568a-358">Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:</span><span class="sxs-lookup"><span data-stu-id="5568a-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5568a-359">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5568a-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="5568a-360">Переключиться на Ньютонсофт.Джсон</span><span class="sxs-lookup"><span data-stu-id="5568a-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="5568a-361">Если вам нужны `Newtonsoft.Json` функции, которые `System.Text.Json`не поддерживаются в , см. [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)</span><span class="sxs-lookup"><span data-stu-id="5568a-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5568a-362">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="5568a-362">MessagePack serialization options</span></span>

<span data-ttu-id="5568a-363">Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5568a-364">Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5568a-365">На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5568a-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5568a-366">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="5568a-366">Configure server options</span></span>

<span data-ttu-id="5568a-367">В следующей таблице описаны варианты настройки концентраторов SignalR:</span><span class="sxs-lookup"><span data-stu-id="5568a-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5568a-368">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-368">Option</span></span> | <span data-ttu-id="5568a-369">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-369">Default Value</span></span> | <span data-ttu-id="5568a-370">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5568a-371">30 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-371">30 seconds</span></span> | <span data-ttu-id="5568a-372">Сервер будет считать клиента отключенным, если он не получил сообщение (включая сохранение жизни) в этом интервале.</span><span class="sxs-lookup"><span data-stu-id="5568a-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5568a-373">Это может занять больше времени, чем этот интервал тайм-аута для клиента на самом деле быть помечены отключены, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="5568a-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5568a-374">Рекомендуемое значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5568a-375">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-375">15 seconds</span></span> | <span data-ttu-id="5568a-376">Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="5568a-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5568a-377">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-378">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5568a-379">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-379">15 seconds</span></span> | <span data-ttu-id="5568a-380">Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="5568a-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5568a-381">При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5568a-382">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5568a-383">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="5568a-383">All installed protocols</span></span> | <span data-ttu-id="5568a-384">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="5568a-384">Protocols supported by this hub.</span></span> <span data-ttu-id="5568a-385">По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="5568a-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5568a-386">Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5568a-387">По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="5568a-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="5568a-388">Максимальное количество элементов, которые могут быть буферизированы для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="5568a-389">Если этот предел достигнут, обработка вызовов блокируется до тех пор, пока сервер не обрабатывает элементы потока.</span><span class="sxs-lookup"><span data-stu-id="5568a-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="5568a-390">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-390">32 KB</span></span> | <span data-ttu-id="5568a-391">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="5568a-392">Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .</span><span class="sxs-lookup"><span data-stu-id="5568a-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5568a-393">Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:</span><span class="sxs-lookup"><span data-stu-id="5568a-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5568a-394">Расширенные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="5568a-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5568a-395">Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5568a-396">Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в .</span><span class="sxs-lookup"><span data-stu-id="5568a-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="5568a-397">В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="5568a-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5568a-398">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-398">Option</span></span> | <span data-ttu-id="5568a-399">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-399">Default Value</span></span> | <span data-ttu-id="5568a-400">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5568a-401">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-401">32 KB</span></span> | <span data-ttu-id="5568a-402">Максимальное количество байтов, полученных от клиента, который буферизирует сервер перед применением бэкпрессии.</span><span class="sxs-lookup"><span data-stu-id="5568a-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="5568a-403">Увеличение этого значения позволяет серверу получать большие сообщения быстрее, не применяя бэкпрессу, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5568a-404">Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5568a-405">Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору.</span><span class="sxs-lookup"><span data-stu-id="5568a-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5568a-406">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-406">32 KB</span></span> | <span data-ttu-id="5568a-407">Максимальное количество байтов, отправленных приложением, которое буферизирует сервер перед наблюдением за бэкпрессионом.</span><span class="sxs-lookup"><span data-stu-id="5568a-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="5568a-408">Увеличение этого значения позволяет серверу буферизировать большие сообщения быстрее, не ожидая избыточного давления, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5568a-409">Все транспортные перевозки включены.</span><span class="sxs-lookup"><span data-stu-id="5568a-409">All Transports are enabled.</span></span> | <span data-ttu-id="5568a-410">Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="5568a-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5568a-411">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="5568a-411">See below.</span></span> | <span data-ttu-id="5568a-412">Дополнительные опции, специфичные для транспорта Long Polling.</span><span class="sxs-lookup"><span data-stu-id="5568a-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5568a-413">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="5568a-413">See below.</span></span> | <span data-ttu-id="5568a-414">Дополнительные опции, специфичныдля для транспорта WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="5568a-415">Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="5568a-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5568a-416">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-416">Option</span></span> | <span data-ttu-id="5568a-417">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-417">Default Value</span></span> | <span data-ttu-id="5568a-418">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5568a-419">90 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-419">90 seconds</span></span> | <span data-ttu-id="5568a-420">Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5568a-421">Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5568a-422">Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="5568a-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5568a-423">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-423">Option</span></span> | <span data-ttu-id="5568a-424">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-424">Default Value</span></span> | <span data-ttu-id="5568a-425">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5568a-426">5 с</span><span class="sxs-lookup"><span data-stu-id="5568a-426">5 seconds</span></span> | <span data-ttu-id="5568a-427">После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается.</span><span class="sxs-lookup"><span data-stu-id="5568a-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5568a-428">Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5568a-429">Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5568a-430">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="5568a-430">Configure client options</span></span>

<span data-ttu-id="5568a-431">Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5568a-432">Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.</span><span class="sxs-lookup"><span data-stu-id="5568a-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5568a-433">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="5568a-433">Configure logging</span></span>

<span data-ttu-id="5568a-434">С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование.</span><span class="sxs-lookup"><span data-stu-id="5568a-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5568a-435">Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="5568a-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5568a-436">Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.</span><span class="sxs-lookup"><span data-stu-id="5568a-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5568a-437">Для регистрации поставщиков logging необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="5568a-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5568a-438">Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.</span><span class="sxs-lookup"><span data-stu-id="5568a-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5568a-439">Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="5568a-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5568a-440">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="5568a-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5568a-441">В клиенте JavaScript `configureLogging` существует аналогичный метод.</span><span class="sxs-lookup"><span data-stu-id="5568a-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5568a-442">Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения.</span><span class="sxs-lookup"><span data-stu-id="5568a-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5568a-443">Входы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="5568a-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="5568a-444">Вместо `LogLevel` значения можно также предоставить `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="5568a-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="5568a-445">Это полезно при настройке регистрации SignalR в средах, где `LogLevel` у вас нет доступа к константам.</span><span class="sxs-lookup"><span data-stu-id="5568a-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="5568a-446">В следующей таблице перечислены доступные уровни журнала.</span><span class="sxs-lookup"><span data-stu-id="5568a-446">The following table lists the available log levels.</span></span> <span data-ttu-id="5568a-447">Значение, которое `configureLogging` вы предоставляете для набора **минимального** уровня журнала, который будет зарегистрирован.</span><span class="sxs-lookup"><span data-stu-id="5568a-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="5568a-448">Сообщения, зарегистрированные на этом уровне, **или уровни, перечисленные после него в таблице,** будут зарегистрированы.</span><span class="sxs-lookup"><span data-stu-id="5568a-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="5568a-449">Строка</span><span class="sxs-lookup"><span data-stu-id="5568a-449">String</span></span>                      | <span data-ttu-id="5568a-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="5568a-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="5568a-451">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="5568a-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="5568a-452">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="5568a-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="5568a-453">Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="5568a-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5568a-454">Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.</span><span class="sxs-lookup"><span data-stu-id="5568a-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5568a-455">Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="5568a-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5568a-456">Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации.</span><span class="sxs-lookup"><span data-stu-id="5568a-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5568a-457">Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="5568a-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5568a-458">Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="5568a-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5568a-459">Это можно смело игнорировать.</span><span class="sxs-lookup"><span data-stu-id="5568a-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5568a-460">Настройка разрешенных перевозок</span><span class="sxs-lookup"><span data-stu-id="5568a-460">Configure allowed transports</span></span>

<span data-ttu-id="5568a-461">Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5568a-462">Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки.</span><span class="sxs-lookup"><span data-stu-id="5568a-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5568a-463">Все переносы включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5568a-463">All transports are enabled by default.</span></span>

<span data-ttu-id="5568a-464">Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:</span><span class="sxs-lookup"><span data-stu-id="5568a-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5568a-465">В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="5568a-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="5568a-466">В этой версии веб-сокететов клиентов Java является единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="5568a-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="5568a-467">В клиенте Java транспорт выбирается `withTransport` с помощью метода `HttpHubConnectionBuilder`на .</span><span class="sxs-lookup"><span data-stu-id="5568a-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="5568a-468">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5568a-469">Клиент SignalR Java пока не поддерживает резервное копирование транспорта.</span><span class="sxs-lookup"><span data-stu-id="5568a-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5568a-470">Настройка аутентификации носителей</span><span class="sxs-lookup"><span data-stu-id="5568a-470">Configure bearer authentication</span></span>

<span data-ttu-id="5568a-471">Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа.</span><span class="sxs-lookup"><span data-stu-id="5568a-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5568a-472">В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом).</span><span class="sxs-lookup"><span data-stu-id="5568a-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5568a-473">В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5568a-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5568a-474">В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5568a-475">В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="5568a-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5568a-476">В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:</span><span class="sxs-lookup"><span data-stu-id="5568a-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="5568a-477">В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="5568a-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5568a-478">Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="5568a-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5568a-479">С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5568a-480">Настройка тайм-аута и опций keep-alive</span><span class="sxs-lookup"><span data-stu-id="5568a-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5568a-481">Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:</span><span class="sxs-lookup"><span data-stu-id="5568a-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5568a-482">.NET</span><span class="sxs-lookup"><span data-stu-id="5568a-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5568a-483">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-483">Option</span></span> | <span data-ttu-id="5568a-484">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-484">Default value</span></span> | <span data-ttu-id="5568a-485">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5568a-486">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-487">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-487">Timeout for server activity.</span></span> <span data-ttu-id="5568a-488">Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5568a-489">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-490">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5568a-491">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-491">15 seconds</span></span> | <span data-ttu-id="5568a-492">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="5568a-493">Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5568a-494">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-495">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5568a-496">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-496">15 seconds</span></span> | <span data-ttu-id="5568a-497">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-498">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-499">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="5568a-500">В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.</span><span class="sxs-lookup"><span data-stu-id="5568a-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5568a-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5568a-502">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-502">Option</span></span> | <span data-ttu-id="5568a-503">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-503">Default value</span></span> | <span data-ttu-id="5568a-504">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5568a-505">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-506">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-506">Timeout for server activity.</span></span> <span data-ttu-id="5568a-507">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5568a-508">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-509">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="5568a-510">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5568a-511">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-512">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-513">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5568a-514">Java</span><span class="sxs-lookup"><span data-stu-id="5568a-514">Java</span></span>](#tab/java)

| <span data-ttu-id="5568a-515">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-515">Option</span></span> | <span data-ttu-id="5568a-516">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-516">Default value</span></span> | <span data-ttu-id="5568a-517">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5568a-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5568a-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5568a-519">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-520">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-520">Timeout for server activity.</span></span> <span data-ttu-id="5568a-521">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5568a-522">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-523">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5568a-524">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-524">15 seconds</span></span> | <span data-ttu-id="5568a-525">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="5568a-526">Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5568a-527">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-528">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="5568a-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="5568a-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="5568a-530">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5568a-531">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-532">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-533">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5568a-534">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="5568a-534">Configure additional options</span></span>

<span data-ttu-id="5568a-535">Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="5568a-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5568a-536">.NET</span><span class="sxs-lookup"><span data-stu-id="5568a-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5568a-537">Опция .NET</span><span class="sxs-lookup"><span data-stu-id="5568a-537">.NET Option</span></span> |  <span data-ttu-id="5568a-538">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-538">Default value</span></span> | <span data-ttu-id="5568a-539">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5568a-540">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5568a-541">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-542">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-543">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5568a-544">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-544">Empty</span></span> | <span data-ttu-id="5568a-545">Коллекция сертификатов TLS для отправки запросов на аутентификации.</span><span class="sxs-lookup"><span data-stu-id="5568a-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5568a-546">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-546">Empty</span></span> | <span data-ttu-id="5568a-547">Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5568a-548">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-548">Empty</span></span> | <span data-ttu-id="5568a-549">Учетные данные для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5568a-550">5 с</span><span class="sxs-lookup"><span data-stu-id="5568a-550">5 seconds</span></span> | <span data-ttu-id="5568a-551">WebSockets только.</span><span class="sxs-lookup"><span data-stu-id="5568a-551">WebSockets only.</span></span> <span data-ttu-id="5568a-552">Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5568a-553">Если сервер не признает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="5568a-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5568a-554">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-554">Empty</span></span> | <span data-ttu-id="5568a-555">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5568a-556">Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5568a-557">Не используется для подключения WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5568a-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="5568a-558">Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="5568a-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5568a-559">Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="5568a-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5568a-560">**При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="5568a-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5568a-561">Прокси HTTP для использования при отправке запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5568a-562">Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5568a-563">Это позволяет использовать аутентификацию Windows.</span><span class="sxs-lookup"><span data-stu-id="5568a-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5568a-564">Делегат, который может быть использован для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5568a-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5568a-565">Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="5568a-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5568a-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5568a-567">Опция JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-567">JavaScript Option</span></span> | <span data-ttu-id="5568a-568">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-568">Default Value</span></span> | <span data-ttu-id="5568a-569">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5568a-570">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5568a-571">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-572">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-573">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5568a-574">Java</span><span class="sxs-lookup"><span data-stu-id="5568a-574">Java</span></span>](#tab/java)

| <span data-ttu-id="5568a-575">Вариант Java</span><span class="sxs-lookup"><span data-stu-id="5568a-575">Java Option</span></span> | <span data-ttu-id="5568a-576">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-576">Default Value</span></span> | <span data-ttu-id="5568a-577">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5568a-578">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5568a-579">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-580">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-581">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5568a-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5568a-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5568a-583">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-583">Empty</span></span> | <span data-ttu-id="5568a-584">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5568a-585">В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:</span><span class="sxs-lookup"><span data-stu-id="5568a-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5568a-586">В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="5568a-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5568a-587">В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5568a-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5568a-588">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5568a-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5568a-589">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5568a-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5568a-590">ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="5568a-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5568a-591">Каждый протокол имеет параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="5568a-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5568a-592">Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) который может быть добавлен после [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в вашем `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="5568a-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5568a-593">Метод `AddJsonProtocol` принимает делегата, `options` который получает объект.</span><span class="sxs-lookup"><span data-stu-id="5568a-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5568a-594">Свойство [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) на этом `JsonSerializerSettings` объекте является JSON.NET объектом, который может быть использован для настройки последовательности аргументов и значений возврата.</span><span class="sxs-lookup"><span data-stu-id="5568a-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5568a-595">Для получения дополнительной информации смотрите [JSON.NET документацию](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="5568a-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="5568a-596">В качестве примера, чтобы настроить сериализатор, чтобы использовать имена свойств "PascalCase", вместо имен `Startup.ConfigureServices`"camelCase" по умолчанию, используйте следующий код в:</span><span class="sxs-lookup"><span data-stu-id="5568a-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="5568a-597">В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="5568a-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5568a-598">Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:</span><span class="sxs-lookup"><span data-stu-id="5568a-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5568a-599">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5568a-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5568a-600">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="5568a-600">MessagePack serialization options</span></span>

<span data-ttu-id="5568a-601">Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5568a-602">Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5568a-603">На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5568a-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5568a-604">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="5568a-604">Configure server options</span></span>

<span data-ttu-id="5568a-605">В следующей таблице описаны варианты настройки концентраторов SignalR:</span><span class="sxs-lookup"><span data-stu-id="5568a-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5568a-606">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-606">Option</span></span> | <span data-ttu-id="5568a-607">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-607">Default Value</span></span> | <span data-ttu-id="5568a-608">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5568a-609">30 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-609">30 seconds</span></span> | <span data-ttu-id="5568a-610">Сервер будет считать клиента отключенным, если он не получил сообщение (включая сохранение жизни) в этом интервале.</span><span class="sxs-lookup"><span data-stu-id="5568a-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5568a-611">Это может занять больше времени, чем этот интервал тайм-аута для клиента на самом деле быть помечены отключены, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="5568a-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5568a-612">Рекомендуемое значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5568a-613">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-613">15 seconds</span></span> | <span data-ttu-id="5568a-614">Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="5568a-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5568a-615">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-616">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5568a-617">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-617">15 seconds</span></span> | <span data-ttu-id="5568a-618">Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="5568a-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5568a-619">При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5568a-620">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5568a-621">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="5568a-621">All installed protocols</span></span> | <span data-ttu-id="5568a-622">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="5568a-622">Protocols supported by this hub.</span></span> <span data-ttu-id="5568a-623">По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="5568a-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5568a-624">Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5568a-625">По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="5568a-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="5568a-626">Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .</span><span class="sxs-lookup"><span data-stu-id="5568a-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5568a-627">Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:</span><span class="sxs-lookup"><span data-stu-id="5568a-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5568a-628">Расширенные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="5568a-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5568a-629">Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5568a-630">Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в .</span><span class="sxs-lookup"><span data-stu-id="5568a-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="5568a-631">В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="5568a-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5568a-632">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-632">Option</span></span> | <span data-ttu-id="5568a-633">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-633">Default Value</span></span> | <span data-ttu-id="5568a-634">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5568a-635">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-635">32 KB</span></span> | <span data-ttu-id="5568a-636">Максимальное количество байтов, полученных от клиента, буфера сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="5568a-637">Увеличение этого значения позволяет серверу получать большие сообщения, но может негативно повлиять на потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5568a-638">Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5568a-639">Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору.</span><span class="sxs-lookup"><span data-stu-id="5568a-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5568a-640">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-640">32 KB</span></span> | <span data-ttu-id="5568a-641">Максимальное количество байтов, отправленных приложением, которое буферизирует сервер.</span><span class="sxs-lookup"><span data-stu-id="5568a-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="5568a-642">Увеличение этого значения позволяет серверу отправлять большие сообщения, но может негативно повлиять на потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5568a-643">Все транспортные перевозки включены.</span><span class="sxs-lookup"><span data-stu-id="5568a-643">All Transports are enabled.</span></span> | <span data-ttu-id="5568a-644">Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="5568a-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5568a-645">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="5568a-645">See below.</span></span> | <span data-ttu-id="5568a-646">Дополнительные опции, специфичные для транспорта Long Polling.</span><span class="sxs-lookup"><span data-stu-id="5568a-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5568a-647">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="5568a-647">See below.</span></span> | <span data-ttu-id="5568a-648">Дополнительные опции, специфичныдля для транспорта WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="5568a-649">Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="5568a-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5568a-650">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-650">Option</span></span> | <span data-ttu-id="5568a-651">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-651">Default Value</span></span> | <span data-ttu-id="5568a-652">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5568a-653">90 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-653">90 seconds</span></span> | <span data-ttu-id="5568a-654">Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5568a-655">Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5568a-656">Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="5568a-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5568a-657">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-657">Option</span></span> | <span data-ttu-id="5568a-658">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-658">Default Value</span></span> | <span data-ttu-id="5568a-659">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5568a-660">5 с</span><span class="sxs-lookup"><span data-stu-id="5568a-660">5 seconds</span></span> | <span data-ttu-id="5568a-661">После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается.</span><span class="sxs-lookup"><span data-stu-id="5568a-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5568a-662">Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5568a-663">Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5568a-664">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="5568a-664">Configure client options</span></span>

<span data-ttu-id="5568a-665">Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5568a-666">Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.</span><span class="sxs-lookup"><span data-stu-id="5568a-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5568a-667">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="5568a-667">Configure logging</span></span>

<span data-ttu-id="5568a-668">С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование.</span><span class="sxs-lookup"><span data-stu-id="5568a-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5568a-669">Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="5568a-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5568a-670">Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.</span><span class="sxs-lookup"><span data-stu-id="5568a-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5568a-671">Для регистрации поставщиков logging необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="5568a-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5568a-672">Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.</span><span class="sxs-lookup"><span data-stu-id="5568a-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5568a-673">Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="5568a-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5568a-674">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="5568a-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5568a-675">В клиенте JavaScript `configureLogging` существует аналогичный метод.</span><span class="sxs-lookup"><span data-stu-id="5568a-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5568a-676">Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения.</span><span class="sxs-lookup"><span data-stu-id="5568a-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5568a-677">Входы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="5568a-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5568a-678">Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="5568a-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5568a-679">Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.</span><span class="sxs-lookup"><span data-stu-id="5568a-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5568a-680">Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="5568a-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5568a-681">Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации.</span><span class="sxs-lookup"><span data-stu-id="5568a-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5568a-682">Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="5568a-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5568a-683">Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="5568a-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5568a-684">Это можно смело игнорировать.</span><span class="sxs-lookup"><span data-stu-id="5568a-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5568a-685">Настройка разрешенных перевозок</span><span class="sxs-lookup"><span data-stu-id="5568a-685">Configure allowed transports</span></span>

<span data-ttu-id="5568a-686">Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5568a-687">Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки.</span><span class="sxs-lookup"><span data-stu-id="5568a-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5568a-688">Все переносы включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5568a-688">All transports are enabled by default.</span></span>

<span data-ttu-id="5568a-689">Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:</span><span class="sxs-lookup"><span data-stu-id="5568a-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5568a-690">В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="5568a-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="5568a-691">В этой версии веб-сокететов клиентов Java является единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="5568a-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5568a-692">Настройка аутентификации носителей</span><span class="sxs-lookup"><span data-stu-id="5568a-692">Configure bearer authentication</span></span>

<span data-ttu-id="5568a-693">Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа.</span><span class="sxs-lookup"><span data-stu-id="5568a-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5568a-694">В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом).</span><span class="sxs-lookup"><span data-stu-id="5568a-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5568a-695">В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5568a-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5568a-696">В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5568a-697">В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="5568a-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5568a-698">В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:</span><span class="sxs-lookup"><span data-stu-id="5568a-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="5568a-699">В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="5568a-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5568a-700">Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="5568a-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5568a-701">С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5568a-702">Настройка тайм-аута и опций keep-alive</span><span class="sxs-lookup"><span data-stu-id="5568a-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5568a-703">Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:</span><span class="sxs-lookup"><span data-stu-id="5568a-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5568a-704">.NET</span><span class="sxs-lookup"><span data-stu-id="5568a-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5568a-705">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-705">Option</span></span> | <span data-ttu-id="5568a-706">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-706">Default value</span></span> | <span data-ttu-id="5568a-707">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5568a-708">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-709">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-709">Timeout for server activity.</span></span> <span data-ttu-id="5568a-710">Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5568a-711">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-712">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5568a-713">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-713">15 seconds</span></span> | <span data-ttu-id="5568a-714">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="5568a-715">Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5568a-716">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-717">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5568a-718">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-718">15 seconds</span></span> | <span data-ttu-id="5568a-719">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-720">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-721">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="5568a-722">В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.</span><span class="sxs-lookup"><span data-stu-id="5568a-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5568a-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5568a-724">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-724">Option</span></span> | <span data-ttu-id="5568a-725">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-725">Default value</span></span> | <span data-ttu-id="5568a-726">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5568a-727">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-728">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-728">Timeout for server activity.</span></span> <span data-ttu-id="5568a-729">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5568a-730">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-731">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="5568a-732">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5568a-733">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-734">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-735">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5568a-736">Java</span><span class="sxs-lookup"><span data-stu-id="5568a-736">Java</span></span>](#tab/java)

| <span data-ttu-id="5568a-737">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-737">Option</span></span> | <span data-ttu-id="5568a-738">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-738">Default value</span></span> | <span data-ttu-id="5568a-739">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5568a-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5568a-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5568a-741">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-742">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-742">Timeout for server activity.</span></span> <span data-ttu-id="5568a-743">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5568a-744">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-745">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5568a-746">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-746">15 seconds</span></span> | <span data-ttu-id="5568a-747">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="5568a-748">Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5568a-749">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-750">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="5568a-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="5568a-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="5568a-752">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5568a-753">Определяет интервал, с которого клиент отправляет сообщения пинг.</span><span class="sxs-lookup"><span data-stu-id="5568a-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5568a-754">Отправка любого сообщения от клиента сбрасывает таймер в начале интервала.</span><span class="sxs-lookup"><span data-stu-id="5568a-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5568a-755">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="5568a-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5568a-756">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="5568a-756">Configure additional options</span></span>

<span data-ttu-id="5568a-757">Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="5568a-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5568a-758">.NET</span><span class="sxs-lookup"><span data-stu-id="5568a-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5568a-759">Опция .NET</span><span class="sxs-lookup"><span data-stu-id="5568a-759">.NET Option</span></span> |  <span data-ttu-id="5568a-760">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-760">Default value</span></span> | <span data-ttu-id="5568a-761">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5568a-762">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5568a-763">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-764">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-765">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5568a-766">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-766">Empty</span></span> | <span data-ttu-id="5568a-767">Коллекция сертификатов TLS для отправки запросов на аутентификации.</span><span class="sxs-lookup"><span data-stu-id="5568a-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5568a-768">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-768">Empty</span></span> | <span data-ttu-id="5568a-769">Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5568a-770">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-770">Empty</span></span> | <span data-ttu-id="5568a-771">Учетные данные для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5568a-772">5 с</span><span class="sxs-lookup"><span data-stu-id="5568a-772">5 seconds</span></span> | <span data-ttu-id="5568a-773">WebSockets только.</span><span class="sxs-lookup"><span data-stu-id="5568a-773">WebSockets only.</span></span> <span data-ttu-id="5568a-774">Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5568a-775">Если сервер не признает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="5568a-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5568a-776">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-776">Empty</span></span> | <span data-ttu-id="5568a-777">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5568a-778">Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5568a-779">Не используется для подключения WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5568a-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="5568a-780">Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="5568a-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5568a-781">Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="5568a-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5568a-782">**При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="5568a-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5568a-783">Прокси HTTP для использования при отправке запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5568a-784">Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5568a-785">Это позволяет использовать аутентификацию Windows.</span><span class="sxs-lookup"><span data-stu-id="5568a-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5568a-786">Делегат, который может быть использован для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5568a-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5568a-787">Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="5568a-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5568a-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5568a-789">Опция JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-789">JavaScript Option</span></span> | <span data-ttu-id="5568a-790">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-790">Default Value</span></span> | <span data-ttu-id="5568a-791">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5568a-792">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5568a-793">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-794">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-795">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5568a-796">Java</span><span class="sxs-lookup"><span data-stu-id="5568a-796">Java</span></span>](#tab/java)

| <span data-ttu-id="5568a-797">Вариант Java</span><span class="sxs-lookup"><span data-stu-id="5568a-797">Java Option</span></span> | <span data-ttu-id="5568a-798">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-798">Default Value</span></span> | <span data-ttu-id="5568a-799">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5568a-800">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5568a-801">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-802">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-803">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5568a-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5568a-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5568a-805">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-805">Empty</span></span> | <span data-ttu-id="5568a-806">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5568a-807">В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:</span><span class="sxs-lookup"><span data-stu-id="5568a-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5568a-808">В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="5568a-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5568a-809">В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5568a-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5568a-810">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5568a-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5568a-811">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5568a-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5568a-812">ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="5568a-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5568a-813">Каждый протокол имеет параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="5568a-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5568a-814">Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) который может быть добавлен после [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в вашем `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="5568a-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5568a-815">Метод `AddJsonProtocol` принимает делегата, `options` который получает объект.</span><span class="sxs-lookup"><span data-stu-id="5568a-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5568a-816">Свойство [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) на этом `JsonSerializerSettings` объекте является JSON.NET объектом, который может быть использован для настройки последовательности аргументов и значений возврата.</span><span class="sxs-lookup"><span data-stu-id="5568a-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5568a-817">Для получения дополнительной информации смотрите [JSON.NET документацию](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="5568a-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="5568a-818">В качестве примера, чтобы настроить сериализатор, чтобы использовать имена свойств "PascalCase", вместо имен `Startup.ConfigureServices`"camelCase" по умолчанию, используйте следующий код в:</span><span class="sxs-lookup"><span data-stu-id="5568a-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="5568a-819">В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="5568a-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5568a-820">Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:</span><span class="sxs-lookup"><span data-stu-id="5568a-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5568a-821">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5568a-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5568a-822">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="5568a-822">MessagePack serialization options</span></span>

<span data-ttu-id="5568a-823">Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5568a-824">Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="5568a-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5568a-825">На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5568a-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5568a-826">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="5568a-826">Configure server options</span></span>

<span data-ttu-id="5568a-827">В следующей таблице описаны варианты настройки концентраторов SignalR:</span><span class="sxs-lookup"><span data-stu-id="5568a-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5568a-828">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-828">Option</span></span> | <span data-ttu-id="5568a-829">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-829">Default Value</span></span> | <span data-ttu-id="5568a-830">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="5568a-831">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-831">15 seconds</span></span> | <span data-ttu-id="5568a-832">Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="5568a-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5568a-833">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-834">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5568a-835">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-835">15 seconds</span></span> | <span data-ttu-id="5568a-836">Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="5568a-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5568a-837">При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5568a-838">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5568a-839">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="5568a-839">All installed protocols</span></span> | <span data-ttu-id="5568a-840">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="5568a-840">Protocols supported by this hub.</span></span> <span data-ttu-id="5568a-841">По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="5568a-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5568a-842">Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5568a-843">По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="5568a-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="5568a-844">Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .</span><span class="sxs-lookup"><span data-stu-id="5568a-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5568a-845">Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:</span><span class="sxs-lookup"><span data-stu-id="5568a-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5568a-846">Расширенные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="5568a-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5568a-847">Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5568a-848">Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в .</span><span class="sxs-lookup"><span data-stu-id="5568a-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="5568a-849">В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="5568a-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5568a-850">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-850">Option</span></span> | <span data-ttu-id="5568a-851">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-851">Default Value</span></span> | <span data-ttu-id="5568a-852">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5568a-853">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-853">32 KB</span></span> | <span data-ttu-id="5568a-854">Максимальное количество байтов, полученных от клиента, буфера сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="5568a-855">Увеличение этого значения позволяет серверу получать большие сообщения, но может негативно повлиять на потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5568a-856">Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора.</span><span class="sxs-lookup"><span data-stu-id="5568a-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5568a-857">Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору.</span><span class="sxs-lookup"><span data-stu-id="5568a-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5568a-858">32 КБ</span><span class="sxs-lookup"><span data-stu-id="5568a-858">32 KB</span></span> | <span data-ttu-id="5568a-859">Максимальное количество байтов, отправленных приложением, которое буферизирует сервер.</span><span class="sxs-lookup"><span data-stu-id="5568a-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="5568a-860">Увеличение этого значения позволяет серверу отправлять большие сообщения, но может негативно повлиять на потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="5568a-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5568a-861">Все транспортные перевозки включены.</span><span class="sxs-lookup"><span data-stu-id="5568a-861">All Transports are enabled.</span></span> | <span data-ttu-id="5568a-862">Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="5568a-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5568a-863">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="5568a-863">See below.</span></span> | <span data-ttu-id="5568a-864">Дополнительные опции, специфичные для транспорта Long Polling.</span><span class="sxs-lookup"><span data-stu-id="5568a-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5568a-865">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="5568a-865">See below.</span></span> | <span data-ttu-id="5568a-866">Дополнительные опции, специфичныдля для транспорта WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="5568a-867">Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="5568a-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5568a-868">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-868">Option</span></span> | <span data-ttu-id="5568a-869">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-869">Default Value</span></span> | <span data-ttu-id="5568a-870">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5568a-871">90 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-871">90 seconds</span></span> | <span data-ttu-id="5568a-872">Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5568a-873">Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5568a-874">Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="5568a-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5568a-875">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-875">Option</span></span> | <span data-ttu-id="5568a-876">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-876">Default Value</span></span> | <span data-ttu-id="5568a-877">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5568a-878">5 с</span><span class="sxs-lookup"><span data-stu-id="5568a-878">5 seconds</span></span> | <span data-ttu-id="5568a-879">После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается.</span><span class="sxs-lookup"><span data-stu-id="5568a-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5568a-880">Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5568a-881">Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение.</span><span class="sxs-lookup"><span data-stu-id="5568a-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5568a-882">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="5568a-882">Configure client options</span></span>

<span data-ttu-id="5568a-883">Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5568a-884">Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.</span><span class="sxs-lookup"><span data-stu-id="5568a-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5568a-885">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="5568a-885">Configure logging</span></span>

<span data-ttu-id="5568a-886">С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование.</span><span class="sxs-lookup"><span data-stu-id="5568a-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5568a-887">Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="5568a-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5568a-888">Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.</span><span class="sxs-lookup"><span data-stu-id="5568a-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5568a-889">Для регистрации поставщиков logging необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="5568a-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5568a-890">Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.</span><span class="sxs-lookup"><span data-stu-id="5568a-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5568a-891">Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="5568a-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5568a-892">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="5568a-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5568a-893">В клиенте JavaScript `configureLogging` существует аналогичный метод.</span><span class="sxs-lookup"><span data-stu-id="5568a-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5568a-894">Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения.</span><span class="sxs-lookup"><span data-stu-id="5568a-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5568a-895">Входы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="5568a-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5568a-896">Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="5568a-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5568a-897">Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.</span><span class="sxs-lookup"><span data-stu-id="5568a-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5568a-898">Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="5568a-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5568a-899">Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации.</span><span class="sxs-lookup"><span data-stu-id="5568a-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5568a-900">Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="5568a-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5568a-901">Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="5568a-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5568a-902">Это можно смело игнорировать.</span><span class="sxs-lookup"><span data-stu-id="5568a-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5568a-903">Настройка разрешенных перевозок</span><span class="sxs-lookup"><span data-stu-id="5568a-903">Configure allowed transports</span></span>

<span data-ttu-id="5568a-904">Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5568a-905">Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки.</span><span class="sxs-lookup"><span data-stu-id="5568a-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5568a-906">Все переносы включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5568a-906">All transports are enabled by default.</span></span>

<span data-ttu-id="5568a-907">Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:</span><span class="sxs-lookup"><span data-stu-id="5568a-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5568a-908">В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="5568a-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5568a-909">Настройка аутентификации носителей</span><span class="sxs-lookup"><span data-stu-id="5568a-909">Configure bearer authentication</span></span>

<span data-ttu-id="5568a-910">Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа.</span><span class="sxs-lookup"><span data-stu-id="5568a-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5568a-911">В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом).</span><span class="sxs-lookup"><span data-stu-id="5568a-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5568a-912">В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5568a-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5568a-913">В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5568a-914">В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="5568a-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5568a-915">В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:</span><span class="sxs-lookup"><span data-stu-id="5568a-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="5568a-916">В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="5568a-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5568a-917">Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="5568a-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5568a-918">С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.</span><span class="sxs-lookup"><span data-stu-id="5568a-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5568a-919">Настройка тайм-аута и опций keep-alive</span><span class="sxs-lookup"><span data-stu-id="5568a-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5568a-920">Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:</span><span class="sxs-lookup"><span data-stu-id="5568a-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5568a-921">.NET</span><span class="sxs-lookup"><span data-stu-id="5568a-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5568a-922">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-922">Option</span></span> | <span data-ttu-id="5568a-923">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-923">Default value</span></span> | <span data-ttu-id="5568a-924">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5568a-925">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-926">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-926">Timeout for server activity.</span></span> <span data-ttu-id="5568a-927">Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5568a-928">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-929">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5568a-930">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-930">15 seconds</span></span> | <span data-ttu-id="5568a-931">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="5568a-932">Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5568a-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5568a-933">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-934">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="5568a-935">В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.</span><span class="sxs-lookup"><span data-stu-id="5568a-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5568a-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5568a-937">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-937">Option</span></span> | <span data-ttu-id="5568a-938">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-938">Default value</span></span> | <span data-ttu-id="5568a-939">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5568a-940">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-941">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-941">Timeout for server activity.</span></span> <span data-ttu-id="5568a-942">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5568a-943">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-944">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5568a-945">Java</span><span class="sxs-lookup"><span data-stu-id="5568a-945">Java</span></span>](#tab/java)

| <span data-ttu-id="5568a-946">Параметр</span><span class="sxs-lookup"><span data-stu-id="5568a-946">Option</span></span> | <span data-ttu-id="5568a-947">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-947">Default value</span></span> | <span data-ttu-id="5568a-948">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5568a-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5568a-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5568a-950">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="5568a-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5568a-951">Тайм-аут для активности сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-951">Timeout for server activity.</span></span> <span data-ttu-id="5568a-952">Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5568a-953">Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута.</span><span class="sxs-lookup"><span data-stu-id="5568a-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5568a-954">Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше значения сервера, чтобы время для пингов прибыло.</span><span class="sxs-lookup"><span data-stu-id="5568a-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5568a-955">15 секунд</span><span class="sxs-lookup"><span data-stu-id="5568a-955">15 seconds</span></span> | <span data-ttu-id="5568a-956">Тайм-аут для первоначального рукопожатия сервера.</span><span class="sxs-lookup"><span data-stu-id="5568a-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="5568a-957">Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие.</span><span class="sxs-lookup"><span data-stu-id="5568a-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5568a-958">Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="5568a-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5568a-959">Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см.</span><span class="sxs-lookup"><span data-stu-id="5568a-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5568a-960">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="5568a-960">Configure additional options</span></span>

<span data-ttu-id="5568a-961">Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="5568a-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5568a-962">.NET</span><span class="sxs-lookup"><span data-stu-id="5568a-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5568a-963">Опция .NET</span><span class="sxs-lookup"><span data-stu-id="5568a-963">.NET Option</span></span> |  <span data-ttu-id="5568a-964">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-964">Default value</span></span> | <span data-ttu-id="5568a-965">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5568a-966">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5568a-967">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-968">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-969">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5568a-970">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-970">Empty</span></span> | <span data-ttu-id="5568a-971">Коллекция сертификатов TLS для отправки запросов на аутентификации.</span><span class="sxs-lookup"><span data-stu-id="5568a-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5568a-972">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-972">Empty</span></span> | <span data-ttu-id="5568a-973">Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5568a-974">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-974">Empty</span></span> | <span data-ttu-id="5568a-975">Учетные данные для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5568a-976">5 с</span><span class="sxs-lookup"><span data-stu-id="5568a-976">5 seconds</span></span> | <span data-ttu-id="5568a-977">WebSockets только.</span><span class="sxs-lookup"><span data-stu-id="5568a-977">WebSockets only.</span></span> <span data-ttu-id="5568a-978">Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса.</span><span class="sxs-lookup"><span data-stu-id="5568a-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5568a-979">Если сервер не признает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="5568a-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5568a-980">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-980">Empty</span></span> | <span data-ttu-id="5568a-981">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5568a-982">Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5568a-983">Не используется для подключения WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5568a-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="5568a-984">Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="5568a-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5568a-985">Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="5568a-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5568a-986">**При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="5568a-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5568a-987">Прокси HTTP для использования при отправке запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5568a-988">Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5568a-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5568a-989">Это позволяет использовать аутентификацию Windows.</span><span class="sxs-lookup"><span data-stu-id="5568a-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5568a-990">Делегат, который может быть использован для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5568a-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5568a-991">Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="5568a-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5568a-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5568a-993">Опция JavaScript</span><span class="sxs-lookup"><span data-stu-id="5568a-993">JavaScript Option</span></span> | <span data-ttu-id="5568a-994">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-994">Default Value</span></span> | <span data-ttu-id="5568a-995">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5568a-996">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5568a-997">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-998">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-999">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5568a-1000">Java</span><span class="sxs-lookup"><span data-stu-id="5568a-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="5568a-1001">Вариант Java</span><span class="sxs-lookup"><span data-stu-id="5568a-1001">Java Option</span></span> | <span data-ttu-id="5568a-1002">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5568a-1002">Default Value</span></span> | <span data-ttu-id="5568a-1003">Описание</span><span class="sxs-lookup"><span data-stu-id="5568a-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5568a-1004">Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5568a-1005">Установите `true` это, чтобы пропустить шаг переговоров.</span><span class="sxs-lookup"><span data-stu-id="5568a-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5568a-1006">**Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.**</span><span class="sxs-lookup"><span data-stu-id="5568a-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5568a-1007">Эта настройка не может быть включена при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5568a-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5568a-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5568a-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5568a-1009">Empty</span><span class="sxs-lookup"><span data-stu-id="5568a-1009">Empty</span></span> | <span data-ttu-id="5568a-1010">Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP.</span><span class="sxs-lookup"><span data-stu-id="5568a-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5568a-1011">В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:</span><span class="sxs-lookup"><span data-stu-id="5568a-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5568a-1012">В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:</span><span class="sxs-lookup"><span data-stu-id="5568a-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5568a-1013">В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5568a-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5568a-1014">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5568a-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
