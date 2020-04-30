---
title: Конфигурация SignalR ASP.NET Core
author: bradygaster
description: Узнайте, как настроить ASP.NET Core SignalR приложения.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558996"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="9674f-103">Конфигурация SignalR для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9674f-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9674f-104">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9674f-105">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9674f-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9674f-106">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="9674f-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9674f-107">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9674f-108">`AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9674f-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9674f-109">`AddJsonProtocol` Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="9674f-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9674f-110">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> это объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9674f-111">Дополнительные сведения см. в [документации System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9674f-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9674f-112">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9674f-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9674f-113">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9674f-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9674f-114">Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="9674f-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9674f-115">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9674f-116">Переключиться на Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="9674f-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9674f-117">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются `System.Text.Json`в, см. раздел [Переключение на Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9674f-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9674f-118">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-118">MessagePack serialization options</span></span>

<span data-ttu-id="9674f-119">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9674f-120">Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-121">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9674f-122">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="9674f-122">Configure server options</span></span>

<span data-ttu-id="9674f-123">В следующей таблице описаны параметры для настройки концентраторов SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9674f-124">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-124">Option</span></span> | <span data-ttu-id="9674f-125">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-125">Default Value</span></span> | <span data-ttu-id="9674f-126">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9674f-127">30 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-127">30 seconds</span></span> | <span data-ttu-id="9674f-128">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="9674f-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9674f-129">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="9674f-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9674f-130">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9674f-131">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-131">15 seconds</span></span> | <span data-ttu-id="9674f-132">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="9674f-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9674f-133">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-134">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-135">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-135">15 seconds</span></span> | <span data-ttu-id="9674f-136">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="9674f-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9674f-137">При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр.</span><span class="sxs-lookup"><span data-stu-id="9674f-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9674f-138">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9674f-139">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="9674f-139">All installed protocols</span></span> | <span data-ttu-id="9674f-140">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="9674f-140">Protocols supported by this hub.</span></span> <span data-ttu-id="9674f-141">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="9674f-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9674f-142">Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="9674f-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9674f-143">Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="9674f-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9674f-144">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="9674f-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9674f-145">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="9674f-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9674f-146">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-146">32 KB</span></span> | <span data-ttu-id="9674f-147">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="9674f-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9674f-148">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9674f-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9674f-149">Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:</span><span class="sxs-lookup"><span data-stu-id="9674f-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9674f-150">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="9674f-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9674f-151">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9674f-152">Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9674f-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9674f-153">В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9674f-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9674f-154">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-154">Option</span></span> | <span data-ttu-id="9674f-155">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-155">Default Value</span></span> | <span data-ttu-id="9674f-156">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9674f-157">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-157">32 KB</span></span> | <span data-ttu-id="9674f-158">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="9674f-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9674f-159">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9674f-160">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="9674f-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9674f-161">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="9674f-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9674f-162">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-162">32 KB</span></span> | <span data-ttu-id="9674f-163">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="9674f-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9674f-164">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9674f-165">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-165">All Transports are enabled.</span></span> | <span data-ttu-id="9674f-166">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="9674f-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9674f-167">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-167">See below.</span></span> | <span data-ttu-id="9674f-168">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="9674f-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9674f-169">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-169">See below.</span></span> | <span data-ttu-id="9674f-170">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9674f-171">0</span><span class="sxs-lookup"><span data-stu-id="9674f-171">0</span></span> | <span data-ttu-id="9674f-172">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="9674f-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9674f-173">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="9674f-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9674f-174">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9674f-175">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-175">Option</span></span> | <span data-ttu-id="9674f-176">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-176">Default Value</span></span> | <span data-ttu-id="9674f-177">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9674f-178">90 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-178">90 seconds</span></span> | <span data-ttu-id="9674f-179">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9674f-180">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="9674f-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9674f-181">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9674f-182">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-182">Option</span></span> | <span data-ttu-id="9674f-183">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-183">Default Value</span></span> | <span data-ttu-id="9674f-184">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9674f-185">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-185">5 seconds</span></span> | <span data-ttu-id="9674f-186">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="9674f-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9674f-187">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="9674f-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9674f-188">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9674f-189">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="9674f-189">Configure client options</span></span>

<span data-ttu-id="9674f-190">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9674f-191">Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.</span><span class="sxs-lookup"><span data-stu-id="9674f-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9674f-192">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="9674f-192">Configure logging</span></span>

<span data-ttu-id="9674f-193">Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода.</span><span class="sxs-lookup"><span data-stu-id="9674f-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9674f-194">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="9674f-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9674f-195">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="9674f-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-196">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="9674f-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9674f-197">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="9674f-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9674f-198">Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="9674f-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9674f-199">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="9674f-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9674f-200">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="9674f-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9674f-201">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9674f-202">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="9674f-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9674f-203">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9674f-204">Это полезно при настройке ведения журнала SignalR в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="9674f-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9674f-205">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-205">The following table lists the available log levels.</span></span> <span data-ttu-id="9674f-206">Значение, которое вы указываете для `configureLogging` установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="9674f-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9674f-207">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="9674f-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9674f-208">Строка</span><span class="sxs-lookup"><span data-stu-id="9674f-208">String</span></span>                      | <span data-ttu-id="9674f-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9674f-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9674f-210">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="9674f-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9674f-211">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="9674f-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9674f-212">Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="9674f-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9674f-213">Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9674f-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9674f-214">Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9674f-215">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9674f-216">В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9674f-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9674f-217">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="9674f-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9674f-218">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="9674f-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9674f-219">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="9674f-219">Configure allowed transports</span></span>

<span data-ttu-id="9674f-220">Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9674f-221">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="9674f-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9674f-222">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-222">All transports are enabled by default.</span></span>

<span data-ttu-id="9674f-223">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="9674f-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9674f-224">В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9674f-225">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="9674f-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9674f-226">В клиенте Java транспорт выбирается с помощью `withTransport` метода в. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9674f-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9674f-227">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9674f-228">Клиент Java SignalR пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="9674f-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9674f-229">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="9674f-229">Configure bearer authentication</span></span>

<span data-ttu-id="9674f-230">Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="9674f-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9674f-231">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9674f-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9674f-232">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="9674f-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9674f-233">В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9674f-234">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9674f-235">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9674f-236">В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9674f-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9674f-237">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="9674f-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9674f-238">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="9674f-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9674f-239">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="9674f-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9674f-240">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:</span><span class="sxs-lookup"><span data-stu-id="9674f-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-241">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-242">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-242">Option</span></span> | <span data-ttu-id="9674f-243">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-243">Default value</span></span> | <span data-ttu-id="9674f-244">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9674f-245">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-246">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-246">Timeout for server activity.</span></span> <span data-ttu-id="9674f-247">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-248">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-249">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9674f-250">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-250">15 seconds</span></span> | <span data-ttu-id="9674f-251">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-252">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-253">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-254">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-255">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-255">15 seconds</span></span> | <span data-ttu-id="9674f-256">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-257">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-258">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9674f-259">В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9674f-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-261">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-261">Option</span></span> | <span data-ttu-id="9674f-262">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-262">Default value</span></span> | <span data-ttu-id="9674f-263">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9674f-264">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-265">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-265">Timeout for server activity.</span></span> <span data-ttu-id="9674f-266">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9674f-267">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-268">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9674f-269">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9674f-270">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-271">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-272">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-273">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-273">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-274">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-274">Option</span></span> | <span data-ttu-id="9674f-275">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-275">Default value</span></span> | <span data-ttu-id="9674f-276">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9674f-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9674f-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9674f-278">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-279">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-279">Timeout for server activity.</span></span> <span data-ttu-id="9674f-280">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-281">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-282">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9674f-283">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-283">15 seconds</span></span> | <span data-ttu-id="9674f-284">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-285">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-286">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-287">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9674f-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9674f-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9674f-289">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9674f-290">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-291">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-292">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9674f-293">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="9674f-293">Configure additional options</span></span>

<span data-ttu-id="9674f-294">Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="9674f-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-295">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-296">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="9674f-296">.NET Option</span></span> |  <span data-ttu-id="9674f-297">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-297">Default value</span></span> | <span data-ttu-id="9674f-298">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9674f-299">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9674f-300">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-301">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-302">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9674f-303">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-303">Empty</span></span> | <span data-ttu-id="9674f-304">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9674f-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9674f-305">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-305">Empty</span></span> | <span data-ttu-id="9674f-306">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9674f-307">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-307">Empty</span></span> | <span data-ttu-id="9674f-308">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9674f-309">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-309">5 seconds</span></span> | <span data-ttu-id="9674f-310">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-310">WebSockets only.</span></span> <span data-ttu-id="9674f-311">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="9674f-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9674f-312">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="9674f-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9674f-313">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-313">Empty</span></span> | <span data-ttu-id="9674f-314">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9674f-315">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9674f-316">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="9674f-317">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="9674f-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9674f-318">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="9674f-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9674f-319">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="9674f-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9674f-320">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9674f-321">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9674f-322">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="9674f-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9674f-323">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9674f-324">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="9674f-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9674f-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-326">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-326">JavaScript Option</span></span> | <span data-ttu-id="9674f-327">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-327">Default Value</span></span> | <span data-ttu-id="9674f-328">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9674f-329">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9674f-330">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-331">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-332">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="9674f-333">Указывает, будут ли учетные данные отправляться с запросом CORS.</span><span class="sxs-lookup"><span data-stu-id="9674f-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="9674f-334">Служба приложений Azure использует файлы cookie для закрепленных сеансов и требует, чтобы этот параметр был включен правильно.</span><span class="sxs-lookup"><span data-stu-id="9674f-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="9674f-335">Дополнительные сведения об CORS с помощью SignalR см. в <xref:signalr/security#cross-origin-resource-sharing>разделе.</span><span class="sxs-lookup"><span data-stu-id="9674f-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-336">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-336">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-337">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="9674f-337">Java Option</span></span> | <span data-ttu-id="9674f-338">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-338">Default Value</span></span> | <span data-ttu-id="9674f-339">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9674f-340">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9674f-341">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-342">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-343">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9674f-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9674f-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9674f-345">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-345">Empty</span></span> | <span data-ttu-id="9674f-346">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9674f-347">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:</span><span class="sxs-lookup"><span data-stu-id="9674f-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9674f-348">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9674f-349">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9674f-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9674f-350">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9674f-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9674f-351">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9674f-352">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9674f-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9674f-353">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="9674f-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9674f-354">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9674f-355">`AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9674f-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9674f-356">`AddJsonProtocol` Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="9674f-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9674f-357">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> это объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9674f-358">Дополнительные сведения см. в [документации System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9674f-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9674f-359">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9674f-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9674f-360">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9674f-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9674f-361">Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="9674f-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9674f-362">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9674f-363">Переключиться на Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="9674f-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9674f-364">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются `System.Text.Json`в, см. раздел [Переключение на Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9674f-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9674f-365">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-365">MessagePack serialization options</span></span>

<span data-ttu-id="9674f-366">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9674f-367">Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-368">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9674f-369">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="9674f-369">Configure server options</span></span>

<span data-ttu-id="9674f-370">В следующей таблице описаны параметры для настройки концентраторов SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9674f-371">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-371">Option</span></span> | <span data-ttu-id="9674f-372">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-372">Default Value</span></span> | <span data-ttu-id="9674f-373">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9674f-374">30 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-374">30 seconds</span></span> | <span data-ttu-id="9674f-375">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="9674f-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9674f-376">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="9674f-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9674f-377">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9674f-378">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-378">15 seconds</span></span> | <span data-ttu-id="9674f-379">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="9674f-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9674f-380">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-381">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-382">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-382">15 seconds</span></span> | <span data-ttu-id="9674f-383">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="9674f-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9674f-384">При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр.</span><span class="sxs-lookup"><span data-stu-id="9674f-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9674f-385">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9674f-386">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="9674f-386">All installed protocols</span></span> | <span data-ttu-id="9674f-387">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="9674f-387">Protocols supported by this hub.</span></span> <span data-ttu-id="9674f-388">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="9674f-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9674f-389">Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="9674f-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9674f-390">Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="9674f-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9674f-391">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="9674f-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9674f-392">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="9674f-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9674f-393">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-393">32 KB</span></span> | <span data-ttu-id="9674f-394">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="9674f-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9674f-395">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9674f-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9674f-396">Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:</span><span class="sxs-lookup"><span data-stu-id="9674f-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9674f-397">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="9674f-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9674f-398">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9674f-399">Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9674f-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9674f-400">В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9674f-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9674f-401">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-401">Option</span></span> | <span data-ttu-id="9674f-402">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-402">Default Value</span></span> | <span data-ttu-id="9674f-403">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9674f-404">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-404">32 KB</span></span> | <span data-ttu-id="9674f-405">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="9674f-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9674f-406">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9674f-407">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="9674f-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9674f-408">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="9674f-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9674f-409">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-409">32 KB</span></span> | <span data-ttu-id="9674f-410">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="9674f-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9674f-411">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9674f-412">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-412">All Transports are enabled.</span></span> | <span data-ttu-id="9674f-413">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="9674f-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9674f-414">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-414">See below.</span></span> | <span data-ttu-id="9674f-415">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="9674f-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9674f-416">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-416">See below.</span></span> | <span data-ttu-id="9674f-417">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9674f-418">0</span><span class="sxs-lookup"><span data-stu-id="9674f-418">0</span></span> | <span data-ttu-id="9674f-419">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="9674f-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9674f-420">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="9674f-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9674f-421">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9674f-422">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-422">Option</span></span> | <span data-ttu-id="9674f-423">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-423">Default Value</span></span> | <span data-ttu-id="9674f-424">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9674f-425">90 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-425">90 seconds</span></span> | <span data-ttu-id="9674f-426">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9674f-427">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="9674f-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9674f-428">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9674f-429">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-429">Option</span></span> | <span data-ttu-id="9674f-430">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-430">Default Value</span></span> | <span data-ttu-id="9674f-431">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9674f-432">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-432">5 seconds</span></span> | <span data-ttu-id="9674f-433">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="9674f-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9674f-434">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="9674f-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9674f-435">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9674f-436">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="9674f-436">Configure client options</span></span>

<span data-ttu-id="9674f-437">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9674f-438">Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.</span><span class="sxs-lookup"><span data-stu-id="9674f-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9674f-439">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="9674f-439">Configure logging</span></span>

<span data-ttu-id="9674f-440">Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода.</span><span class="sxs-lookup"><span data-stu-id="9674f-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9674f-441">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="9674f-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9674f-442">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="9674f-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-443">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="9674f-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9674f-444">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="9674f-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9674f-445">Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="9674f-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9674f-446">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="9674f-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9674f-447">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="9674f-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9674f-448">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9674f-449">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="9674f-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9674f-450">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9674f-451">Это полезно при настройке ведения журнала SignalR в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="9674f-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9674f-452">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-452">The following table lists the available log levels.</span></span> <span data-ttu-id="9674f-453">Значение, которое вы указываете для `configureLogging` установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="9674f-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9674f-454">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="9674f-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9674f-455">Строка</span><span class="sxs-lookup"><span data-stu-id="9674f-455">String</span></span>                      | <span data-ttu-id="9674f-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9674f-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9674f-457">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="9674f-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9674f-458">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="9674f-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9674f-459">Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="9674f-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9674f-460">Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9674f-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9674f-461">Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9674f-462">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9674f-463">В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9674f-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9674f-464">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="9674f-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9674f-465">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="9674f-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9674f-466">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="9674f-466">Configure allowed transports</span></span>

<span data-ttu-id="9674f-467">Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9674f-468">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="9674f-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9674f-469">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-469">All transports are enabled by default.</span></span>

<span data-ttu-id="9674f-470">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="9674f-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9674f-471">В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9674f-472">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="9674f-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9674f-473">В клиенте Java транспорт выбирается с помощью `withTransport` метода в. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9674f-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9674f-474">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9674f-475">Клиент Java SignalR пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="9674f-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9674f-476">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="9674f-476">Configure bearer authentication</span></span>

<span data-ttu-id="9674f-477">Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="9674f-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9674f-478">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9674f-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9674f-479">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="9674f-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9674f-480">В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9674f-481">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9674f-482">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9674f-483">В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9674f-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9674f-484">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="9674f-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9674f-485">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="9674f-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9674f-486">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="9674f-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9674f-487">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:</span><span class="sxs-lookup"><span data-stu-id="9674f-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-488">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-489">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-489">Option</span></span> | <span data-ttu-id="9674f-490">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-490">Default value</span></span> | <span data-ttu-id="9674f-491">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9674f-492">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-493">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-493">Timeout for server activity.</span></span> <span data-ttu-id="9674f-494">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-495">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-496">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9674f-497">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-497">15 seconds</span></span> | <span data-ttu-id="9674f-498">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-499">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-500">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-501">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-502">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-502">15 seconds</span></span> | <span data-ttu-id="9674f-503">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-504">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-505">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9674f-506">В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9674f-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-508">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-508">Option</span></span> | <span data-ttu-id="9674f-509">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-509">Default value</span></span> | <span data-ttu-id="9674f-510">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9674f-511">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-512">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-512">Timeout for server activity.</span></span> <span data-ttu-id="9674f-513">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9674f-514">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-515">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9674f-516">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9674f-517">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-518">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-519">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-520">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-520">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-521">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-521">Option</span></span> | <span data-ttu-id="9674f-522">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-522">Default value</span></span> | <span data-ttu-id="9674f-523">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9674f-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9674f-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9674f-525">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-526">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-526">Timeout for server activity.</span></span> <span data-ttu-id="9674f-527">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-528">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-529">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9674f-530">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-530">15 seconds</span></span> | <span data-ttu-id="9674f-531">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-532">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-533">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-534">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9674f-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9674f-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9674f-536">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9674f-537">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-538">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-539">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9674f-540">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="9674f-540">Configure additional options</span></span>

<span data-ttu-id="9674f-541">Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="9674f-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-542">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-543">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="9674f-543">.NET Option</span></span> |  <span data-ttu-id="9674f-544">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-544">Default value</span></span> | <span data-ttu-id="9674f-545">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9674f-546">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9674f-547">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-548">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-549">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9674f-550">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-550">Empty</span></span> | <span data-ttu-id="9674f-551">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9674f-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9674f-552">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-552">Empty</span></span> | <span data-ttu-id="9674f-553">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9674f-554">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-554">Empty</span></span> | <span data-ttu-id="9674f-555">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9674f-556">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-556">5 seconds</span></span> | <span data-ttu-id="9674f-557">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-557">WebSockets only.</span></span> <span data-ttu-id="9674f-558">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="9674f-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9674f-559">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="9674f-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9674f-560">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-560">Empty</span></span> | <span data-ttu-id="9674f-561">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9674f-562">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9674f-563">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="9674f-564">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="9674f-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9674f-565">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="9674f-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9674f-566">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="9674f-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9674f-567">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9674f-568">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9674f-569">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="9674f-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9674f-570">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9674f-571">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="9674f-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9674f-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-573">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-573">JavaScript Option</span></span> | <span data-ttu-id="9674f-574">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-574">Default Value</span></span> | <span data-ttu-id="9674f-575">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9674f-576">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9674f-577">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-578">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-579">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-580">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-580">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-581">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="9674f-581">Java Option</span></span> | <span data-ttu-id="9674f-582">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-582">Default Value</span></span> | <span data-ttu-id="9674f-583">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9674f-584">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9674f-585">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-586">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-587">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9674f-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9674f-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9674f-589">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-589">Empty</span></span> | <span data-ttu-id="9674f-590">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9674f-591">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:</span><span class="sxs-lookup"><span data-stu-id="9674f-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9674f-592">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9674f-593">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9674f-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9674f-594">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9674f-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9674f-595">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9674f-596">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9674f-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9674f-597">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="9674f-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9674f-598">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9674f-599">`AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9674f-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9674f-600">`AddJsonProtocol` Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="9674f-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9674f-601">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> это объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9674f-602">Дополнительные сведения см. в [документации System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9674f-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9674f-603">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9674f-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="9674f-604">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9674f-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9674f-605">Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="9674f-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9674f-606">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9674f-607">Переключиться на Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="9674f-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9674f-608">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются `System.Text.Json`в, см. раздел [Переключение на Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9674f-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9674f-609">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-609">MessagePack serialization options</span></span>

<span data-ttu-id="9674f-610">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9674f-611">Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-612">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9674f-613">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="9674f-613">Configure server options</span></span>

<span data-ttu-id="9674f-614">В следующей таблице описаны параметры для настройки концентраторов SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9674f-615">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-615">Option</span></span> | <span data-ttu-id="9674f-616">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-616">Default Value</span></span> | <span data-ttu-id="9674f-617">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9674f-618">30 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-618">30 seconds</span></span> | <span data-ttu-id="9674f-619">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="9674f-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9674f-620">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="9674f-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9674f-621">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9674f-622">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-622">15 seconds</span></span> | <span data-ttu-id="9674f-623">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="9674f-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9674f-624">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-625">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-626">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-626">15 seconds</span></span> | <span data-ttu-id="9674f-627">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="9674f-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9674f-628">При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр.</span><span class="sxs-lookup"><span data-stu-id="9674f-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9674f-629">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9674f-630">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="9674f-630">All installed protocols</span></span> | <span data-ttu-id="9674f-631">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="9674f-631">Protocols supported by this hub.</span></span> <span data-ttu-id="9674f-632">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="9674f-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9674f-633">Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="9674f-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9674f-634">Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="9674f-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9674f-635">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="9674f-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9674f-636">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="9674f-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9674f-637">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-637">32 KB</span></span> | <span data-ttu-id="9674f-638">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="9674f-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9674f-639">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9674f-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9674f-640">Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:</span><span class="sxs-lookup"><span data-stu-id="9674f-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9674f-641">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="9674f-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9674f-642">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9674f-643">Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9674f-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9674f-644">В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9674f-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9674f-645">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-645">Option</span></span> | <span data-ttu-id="9674f-646">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-646">Default Value</span></span> | <span data-ttu-id="9674f-647">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9674f-648">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-648">32 KB</span></span> | <span data-ttu-id="9674f-649">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="9674f-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9674f-650">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9674f-651">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="9674f-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9674f-652">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="9674f-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9674f-653">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-653">32 KB</span></span> | <span data-ttu-id="9674f-654">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="9674f-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9674f-655">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9674f-656">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-656">All Transports are enabled.</span></span> | <span data-ttu-id="9674f-657">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="9674f-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9674f-658">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-658">See below.</span></span> | <span data-ttu-id="9674f-659">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="9674f-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9674f-660">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-660">See below.</span></span> | <span data-ttu-id="9674f-661">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9674f-662">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9674f-663">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-663">Option</span></span> | <span data-ttu-id="9674f-664">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-664">Default Value</span></span> | <span data-ttu-id="9674f-665">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9674f-666">90 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-666">90 seconds</span></span> | <span data-ttu-id="9674f-667">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9674f-668">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="9674f-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9674f-669">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9674f-670">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-670">Option</span></span> | <span data-ttu-id="9674f-671">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-671">Default Value</span></span> | <span data-ttu-id="9674f-672">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9674f-673">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-673">5 seconds</span></span> | <span data-ttu-id="9674f-674">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="9674f-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9674f-675">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="9674f-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9674f-676">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9674f-677">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="9674f-677">Configure client options</span></span>

<span data-ttu-id="9674f-678">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9674f-679">Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.</span><span class="sxs-lookup"><span data-stu-id="9674f-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9674f-680">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="9674f-680">Configure logging</span></span>

<span data-ttu-id="9674f-681">Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода.</span><span class="sxs-lookup"><span data-stu-id="9674f-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9674f-682">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="9674f-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9674f-683">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="9674f-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-684">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="9674f-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9674f-685">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="9674f-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9674f-686">Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="9674f-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9674f-687">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="9674f-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9674f-688">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="9674f-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9674f-689">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9674f-690">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="9674f-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9674f-691">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9674f-692">Это полезно при настройке ведения журнала SignalR в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="9674f-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9674f-693">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-693">The following table lists the available log levels.</span></span> <span data-ttu-id="9674f-694">Значение, которое вы указываете для `configureLogging` установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="9674f-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9674f-695">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="9674f-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9674f-696">Строка</span><span class="sxs-lookup"><span data-stu-id="9674f-696">String</span></span>                      | <span data-ttu-id="9674f-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9674f-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9674f-698">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="9674f-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9674f-699">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="9674f-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9674f-700">Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="9674f-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9674f-701">Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9674f-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9674f-702">Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9674f-703">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9674f-704">В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9674f-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9674f-705">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="9674f-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9674f-706">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="9674f-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9674f-707">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="9674f-707">Configure allowed transports</span></span>

<span data-ttu-id="9674f-708">Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9674f-709">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="9674f-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9674f-710">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-710">All transports are enabled by default.</span></span>

<span data-ttu-id="9674f-711">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="9674f-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9674f-712">В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9674f-713">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="9674f-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9674f-714">В клиенте Java транспорт выбирается с помощью `withTransport` метода в. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9674f-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9674f-715">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9674f-716">Клиент Java SignalR пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="9674f-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9674f-717">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="9674f-717">Configure bearer authentication</span></span>

<span data-ttu-id="9674f-718">Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="9674f-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9674f-719">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9674f-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9674f-720">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="9674f-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9674f-721">В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9674f-722">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9674f-723">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9674f-724">В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9674f-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9674f-725">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="9674f-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9674f-726">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="9674f-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9674f-727">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="9674f-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9674f-728">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:</span><span class="sxs-lookup"><span data-stu-id="9674f-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-729">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-730">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-730">Option</span></span> | <span data-ttu-id="9674f-731">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-731">Default value</span></span> | <span data-ttu-id="9674f-732">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9674f-733">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-734">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-734">Timeout for server activity.</span></span> <span data-ttu-id="9674f-735">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-736">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-737">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9674f-738">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-738">15 seconds</span></span> | <span data-ttu-id="9674f-739">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-740">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-741">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-742">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-743">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-743">15 seconds</span></span> | <span data-ttu-id="9674f-744">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-745">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-746">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9674f-747">В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9674f-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-749">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-749">Option</span></span> | <span data-ttu-id="9674f-750">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-750">Default value</span></span> | <span data-ttu-id="9674f-751">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9674f-752">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-753">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-753">Timeout for server activity.</span></span> <span data-ttu-id="9674f-754">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9674f-755">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-756">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9674f-757">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9674f-758">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-759">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-760">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-761">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-761">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-762">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-762">Option</span></span> | <span data-ttu-id="9674f-763">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-763">Default value</span></span> | <span data-ttu-id="9674f-764">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9674f-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9674f-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9674f-766">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-767">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-767">Timeout for server activity.</span></span> <span data-ttu-id="9674f-768">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-769">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-770">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9674f-771">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-771">15 seconds</span></span> | <span data-ttu-id="9674f-772">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-773">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-774">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-775">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9674f-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9674f-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9674f-777">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9674f-778">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-779">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-780">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9674f-781">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="9674f-781">Configure additional options</span></span>

<span data-ttu-id="9674f-782">Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="9674f-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-783">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-784">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="9674f-784">.NET Option</span></span> |  <span data-ttu-id="9674f-785">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-785">Default value</span></span> | <span data-ttu-id="9674f-786">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9674f-787">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9674f-788">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-789">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-790">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9674f-791">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-791">Empty</span></span> | <span data-ttu-id="9674f-792">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9674f-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9674f-793">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-793">Empty</span></span> | <span data-ttu-id="9674f-794">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9674f-795">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-795">Empty</span></span> | <span data-ttu-id="9674f-796">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9674f-797">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-797">5 seconds</span></span> | <span data-ttu-id="9674f-798">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-798">WebSockets only.</span></span> <span data-ttu-id="9674f-799">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="9674f-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9674f-800">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="9674f-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9674f-801">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-801">Empty</span></span> | <span data-ttu-id="9674f-802">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9674f-803">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9674f-804">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="9674f-805">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="9674f-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9674f-806">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="9674f-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9674f-807">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="9674f-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9674f-808">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9674f-809">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9674f-810">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="9674f-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9674f-811">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9674f-812">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="9674f-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9674f-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-814">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-814">JavaScript Option</span></span> | <span data-ttu-id="9674f-815">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-815">Default Value</span></span> | <span data-ttu-id="9674f-816">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9674f-817">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9674f-818">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-819">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-820">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-821">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-821">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-822">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="9674f-822">Java Option</span></span> | <span data-ttu-id="9674f-823">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-823">Default Value</span></span> | <span data-ttu-id="9674f-824">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9674f-825">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9674f-826">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-827">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-828">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9674f-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9674f-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9674f-830">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-830">Empty</span></span> | <span data-ttu-id="9674f-831">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9674f-832">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:</span><span class="sxs-lookup"><span data-stu-id="9674f-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9674f-833">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9674f-834">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9674f-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9674f-835">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9674f-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9674f-836">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9674f-837">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9674f-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9674f-838">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="9674f-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9674f-839">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="9674f-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9674f-840">`AddJsonProtocol` Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="9674f-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9674f-841">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является объектом JSON.NET `JsonSerializerSettings` , который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9674f-842">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="9674f-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9674f-843">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9674f-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9674f-844">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9674f-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9674f-845">Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="9674f-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9674f-846">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9674f-847">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-847">MessagePack serialization options</span></span>

<span data-ttu-id="9674f-848">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9674f-849">Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-850">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9674f-851">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="9674f-851">Configure server options</span></span>

<span data-ttu-id="9674f-852">В следующей таблице описаны параметры для настройки концентраторов SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9674f-853">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-853">Option</span></span> | <span data-ttu-id="9674f-854">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-854">Default Value</span></span> | <span data-ttu-id="9674f-855">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9674f-856">30 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-856">30 seconds</span></span> | <span data-ttu-id="9674f-857">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="9674f-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9674f-858">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="9674f-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9674f-859">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9674f-860">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-860">15 seconds</span></span> | <span data-ttu-id="9674f-861">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="9674f-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9674f-862">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-863">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-864">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-864">15 seconds</span></span> | <span data-ttu-id="9674f-865">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="9674f-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9674f-866">При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр.</span><span class="sxs-lookup"><span data-stu-id="9674f-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9674f-867">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9674f-868">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="9674f-868">All installed protocols</span></span> | <span data-ttu-id="9674f-869">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="9674f-869">Protocols supported by this hub.</span></span> <span data-ttu-id="9674f-870">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="9674f-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9674f-871">Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="9674f-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9674f-872">Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="9674f-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9674f-873">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9674f-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9674f-874">Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:</span><span class="sxs-lookup"><span data-stu-id="9674f-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9674f-875">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="9674f-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9674f-876">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9674f-877">Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9674f-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9674f-878">В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9674f-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9674f-879">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-879">Option</span></span> | <span data-ttu-id="9674f-880">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-880">Default Value</span></span> | <span data-ttu-id="9674f-881">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9674f-882">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-882">32 KB</span></span> | <span data-ttu-id="9674f-883">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9674f-884">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9674f-885">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="9674f-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9674f-886">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="9674f-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9674f-887">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-887">32 KB</span></span> | <span data-ttu-id="9674f-888">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9674f-889">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9674f-890">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-890">All Transports are enabled.</span></span> | <span data-ttu-id="9674f-891">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="9674f-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9674f-892">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-892">See below.</span></span> | <span data-ttu-id="9674f-893">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="9674f-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9674f-894">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-894">See below.</span></span> | <span data-ttu-id="9674f-895">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9674f-896">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9674f-897">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-897">Option</span></span> | <span data-ttu-id="9674f-898">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-898">Default Value</span></span> | <span data-ttu-id="9674f-899">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9674f-900">90 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-900">90 seconds</span></span> | <span data-ttu-id="9674f-901">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9674f-902">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="9674f-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9674f-903">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9674f-904">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-904">Option</span></span> | <span data-ttu-id="9674f-905">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-905">Default Value</span></span> | <span data-ttu-id="9674f-906">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9674f-907">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-907">5 seconds</span></span> | <span data-ttu-id="9674f-908">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="9674f-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9674f-909">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="9674f-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9674f-910">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9674f-911">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="9674f-911">Configure client options</span></span>

<span data-ttu-id="9674f-912">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9674f-913">Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.</span><span class="sxs-lookup"><span data-stu-id="9674f-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9674f-914">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="9674f-914">Configure logging</span></span>

<span data-ttu-id="9674f-915">Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода.</span><span class="sxs-lookup"><span data-stu-id="9674f-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9674f-916">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="9674f-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9674f-917">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="9674f-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-918">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="9674f-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9674f-919">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="9674f-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9674f-920">Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="9674f-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9674f-921">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="9674f-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9674f-922">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="9674f-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9674f-923">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9674f-924">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="9674f-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9674f-925">Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="9674f-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9674f-926">Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9674f-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9674f-927">Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9674f-928">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9674f-929">В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9674f-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9674f-930">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="9674f-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9674f-931">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="9674f-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9674f-932">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="9674f-932">Configure allowed transports</span></span>

<span data-ttu-id="9674f-933">Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9674f-934">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="9674f-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9674f-935">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-935">All transports are enabled by default.</span></span>

<span data-ttu-id="9674f-936">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="9674f-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9674f-937">В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9674f-938">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="9674f-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9674f-939">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="9674f-939">Configure bearer authentication</span></span>

<span data-ttu-id="9674f-940">Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="9674f-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9674f-941">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9674f-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9674f-942">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="9674f-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9674f-943">В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9674f-944">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9674f-945">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9674f-946">В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9674f-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9674f-947">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="9674f-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9674f-948">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="9674f-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9674f-949">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="9674f-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9674f-950">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:</span><span class="sxs-lookup"><span data-stu-id="9674f-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-951">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-952">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-952">Option</span></span> | <span data-ttu-id="9674f-953">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-953">Default value</span></span> | <span data-ttu-id="9674f-954">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9674f-955">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-956">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-956">Timeout for server activity.</span></span> <span data-ttu-id="9674f-957">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-958">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-959">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9674f-960">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-960">15 seconds</span></span> | <span data-ttu-id="9674f-961">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-962">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-963">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-964">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-965">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-965">15 seconds</span></span> | <span data-ttu-id="9674f-966">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-967">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-968">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9674f-969">В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9674f-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-971">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-971">Option</span></span> | <span data-ttu-id="9674f-972">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-972">Default value</span></span> | <span data-ttu-id="9674f-973">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9674f-974">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-975">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-975">Timeout for server activity.</span></span> <span data-ttu-id="9674f-976">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9674f-977">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-978">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9674f-979">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9674f-980">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-981">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-982">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-983">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-983">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-984">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-984">Option</span></span> | <span data-ttu-id="9674f-985">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-985">Default value</span></span> | <span data-ttu-id="9674f-986">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9674f-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9674f-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9674f-988">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-989">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-989">Timeout for server activity.</span></span> <span data-ttu-id="9674f-990">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-991">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-992">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9674f-993">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-993">15 seconds</span></span> | <span data-ttu-id="9674f-994">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-995">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-996">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-997">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9674f-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9674f-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9674f-999">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9674f-1000">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9674f-1001">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="9674f-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9674f-1002">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="9674f-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9674f-1003">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="9674f-1003">Configure additional options</span></span>

<span data-ttu-id="9674f-1004">Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="9674f-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-1006">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="9674f-1006">.NET Option</span></span> |  <span data-ttu-id="9674f-1007">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1007">Default value</span></span> | <span data-ttu-id="9674f-1008">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9674f-1009">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9674f-1010">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-1011">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-1012">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9674f-1013">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1013">Empty</span></span> | <span data-ttu-id="9674f-1014">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9674f-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9674f-1015">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1015">Empty</span></span> | <span data-ttu-id="9674f-1016">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9674f-1017">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1017">Empty</span></span> | <span data-ttu-id="9674f-1018">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9674f-1019">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-1019">5 seconds</span></span> | <span data-ttu-id="9674f-1020">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-1020">WebSockets only.</span></span> <span data-ttu-id="9674f-1021">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="9674f-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9674f-1022">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="9674f-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9674f-1023">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1023">Empty</span></span> | <span data-ttu-id="9674f-1024">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9674f-1025">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9674f-1026">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="9674f-1027">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="9674f-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9674f-1028">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="9674f-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9674f-1029">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="9674f-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9674f-1030">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9674f-1031">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9674f-1032">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="9674f-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9674f-1033">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9674f-1034">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="9674f-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9674f-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-1036">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-1036">JavaScript Option</span></span> | <span data-ttu-id="9674f-1037">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1037">Default Value</span></span> | <span data-ttu-id="9674f-1038">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9674f-1039">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9674f-1040">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-1041">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-1042">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-1043">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-1044">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="9674f-1044">Java Option</span></span> | <span data-ttu-id="9674f-1045">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1045">Default Value</span></span> | <span data-ttu-id="9674f-1046">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9674f-1047">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9674f-1048">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-1049">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-1050">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9674f-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9674f-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9674f-1052">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1052">Empty</span></span> | <span data-ttu-id="9674f-1053">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9674f-1054">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:</span><span class="sxs-lookup"><span data-stu-id="9674f-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9674f-1055">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9674f-1056">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9674f-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9674f-1057">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9674f-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9674f-1058">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9674f-1059">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9674f-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9674f-1060">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="9674f-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9674f-1061">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="9674f-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9674f-1062">`AddJsonProtocol` Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="9674f-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9674f-1063">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является объектом JSON.NET `JsonSerializerSettings` , который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9674f-1064">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="9674f-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9674f-1065">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9674f-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9674f-1066">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9674f-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9674f-1067">Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="9674f-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9674f-1068">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9674f-1069">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="9674f-1069">MessagePack serialization options</span></span>

<span data-ttu-id="9674f-1070">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9674f-1071">Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9674f-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-1072">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9674f-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9674f-1073">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="9674f-1073">Configure server options</span></span>

<span data-ttu-id="9674f-1074">В следующей таблице описаны параметры для настройки концентраторов SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9674f-1075">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-1075">Option</span></span> | <span data-ttu-id="9674f-1076">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1076">Default Value</span></span> | <span data-ttu-id="9674f-1077">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9674f-1078">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-1078">15 seconds</span></span> | <span data-ttu-id="9674f-1079">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="9674f-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9674f-1080">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-1081">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9674f-1082">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-1082">15 seconds</span></span> | <span data-ttu-id="9674f-1083">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="9674f-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9674f-1084">При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр.</span><span class="sxs-lookup"><span data-stu-id="9674f-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9674f-1085">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9674f-1086">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="9674f-1086">All installed protocols</span></span> | <span data-ttu-id="9674f-1087">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="9674f-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="9674f-1088">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="9674f-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9674f-1089">Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="9674f-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9674f-1090">Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="9674f-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9674f-1091">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9674f-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9674f-1092">Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:</span><span class="sxs-lookup"><span data-stu-id="9674f-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9674f-1093">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="9674f-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9674f-1094">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9674f-1095">Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9674f-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9674f-1096">В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9674f-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9674f-1097">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-1097">Option</span></span> | <span data-ttu-id="9674f-1098">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1098">Default Value</span></span> | <span data-ttu-id="9674f-1099">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9674f-1100">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-1100">32 KB</span></span> | <span data-ttu-id="9674f-1101">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9674f-1102">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9674f-1103">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="9674f-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9674f-1104">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="9674f-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9674f-1105">32 КБ</span><span class="sxs-lookup"><span data-stu-id="9674f-1105">32 KB</span></span> | <span data-ttu-id="9674f-1106">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9674f-1107">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="9674f-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9674f-1108">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-1108">All Transports are enabled.</span></span> | <span data-ttu-id="9674f-1109">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="9674f-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9674f-1110">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-1110">See below.</span></span> | <span data-ttu-id="9674f-1111">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="9674f-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9674f-1112">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="9674f-1112">See below.</span></span> | <span data-ttu-id="9674f-1113">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9674f-1114">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9674f-1115">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-1115">Option</span></span> | <span data-ttu-id="9674f-1116">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1116">Default Value</span></span> | <span data-ttu-id="9674f-1117">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9674f-1118">90 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-1118">90 seconds</span></span> | <span data-ttu-id="9674f-1119">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9674f-1120">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="9674f-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9674f-1121">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:</span><span class="sxs-lookup"><span data-stu-id="9674f-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9674f-1122">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-1122">Option</span></span> | <span data-ttu-id="9674f-1123">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1123">Default Value</span></span> | <span data-ttu-id="9674f-1124">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9674f-1125">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-1125">5 seconds</span></span> | <span data-ttu-id="9674f-1126">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="9674f-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9674f-1127">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="9674f-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9674f-1128">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="9674f-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9674f-1129">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="9674f-1129">Configure client options</span></span>

<span data-ttu-id="9674f-1130">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9674f-1131">Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.</span><span class="sxs-lookup"><span data-stu-id="9674f-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9674f-1132">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="9674f-1132">Configure logging</span></span>

<span data-ttu-id="9674f-1133">Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода.</span><span class="sxs-lookup"><span data-stu-id="9674f-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9674f-1134">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="9674f-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9674f-1135">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="9674f-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9674f-1136">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="9674f-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9674f-1137">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="9674f-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9674f-1138">Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="9674f-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9674f-1139">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="9674f-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9674f-1140">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="9674f-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9674f-1141">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9674f-1142">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="9674f-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9674f-1143">Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="9674f-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9674f-1144">Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9674f-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9674f-1145">Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9674f-1146">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9674f-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9674f-1147">В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9674f-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9674f-1148">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="9674f-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9674f-1149">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="9674f-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9674f-1150">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="9674f-1150">Configure allowed transports</span></span>

<span data-ttu-id="9674f-1151">Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9674f-1152">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="9674f-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9674f-1153">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="9674f-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="9674f-1154">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="9674f-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9674f-1155">В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9674f-1156">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="9674f-1156">Configure bearer authentication</span></span>

<span data-ttu-id="9674f-1157">Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="9674f-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9674f-1158">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9674f-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9674f-1159">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="9674f-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9674f-1160">В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9674f-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9674f-1161">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9674f-1162">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="9674f-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9674f-1163">В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9674f-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9674f-1164">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="9674f-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9674f-1165">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="9674f-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9674f-1166">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="9674f-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9674f-1167">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:</span><span class="sxs-lookup"><span data-stu-id="9674f-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-1169">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-1169">Option</span></span> | <span data-ttu-id="9674f-1170">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1170">Default value</span></span> | <span data-ttu-id="9674f-1171">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9674f-1172">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-1173">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-1173">Timeout for server activity.</span></span> <span data-ttu-id="9674f-1174">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-1175">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-1176">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9674f-1177">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-1177">15 seconds</span></span> | <span data-ttu-id="9674f-1178">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-1179">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9674f-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9674f-1180">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-1181">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9674f-1182">В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.</span><span class="sxs-lookup"><span data-stu-id="9674f-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9674f-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-1184">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-1184">Option</span></span> | <span data-ttu-id="9674f-1185">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1185">Default value</span></span> | <span data-ttu-id="9674f-1186">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9674f-1187">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-1188">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-1188">Timeout for server activity.</span></span> <span data-ttu-id="9674f-1189">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9674f-1190">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-1191">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-1192">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-1193">Параметр</span><span class="sxs-lookup"><span data-stu-id="9674f-1193">Option</span></span> | <span data-ttu-id="9674f-1194">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1194">Default value</span></span> | <span data-ttu-id="9674f-1195">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9674f-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9674f-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9674f-1197">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="9674f-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9674f-1198">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-1198">Timeout for server activity.</span></span> <span data-ttu-id="9674f-1199">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-1200">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="9674f-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9674f-1201">Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, которое позволяет получить время для проверки связи.</span><span class="sxs-lookup"><span data-stu-id="9674f-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9674f-1202">15 секунд</span><span class="sxs-lookup"><span data-stu-id="9674f-1202">15 seconds</span></span> | <span data-ttu-id="9674f-1203">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="9674f-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="9674f-1204">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="9674f-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9674f-1205">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="9674f-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9674f-1206">Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9674f-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9674f-1207">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="9674f-1207">Configure additional options</span></span>

<span data-ttu-id="9674f-1208">Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="9674f-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9674f-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="9674f-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9674f-1210">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="9674f-1210">.NET Option</span></span> |  <span data-ttu-id="9674f-1211">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1211">Default value</span></span> | <span data-ttu-id="9674f-1212">Описание:</span><span class="sxs-lookup"><span data-stu-id="9674f-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9674f-1213">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9674f-1214">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-1215">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-1216">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9674f-1217">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1217">Empty</span></span> | <span data-ttu-id="9674f-1218">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9674f-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9674f-1219">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1219">Empty</span></span> | <span data-ttu-id="9674f-1220">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9674f-1221">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1221">Empty</span></span> | <span data-ttu-id="9674f-1222">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9674f-1223">5 с</span><span class="sxs-lookup"><span data-stu-id="9674f-1223">5 seconds</span></span> | <span data-ttu-id="9674f-1224">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-1224">WebSockets only.</span></span> <span data-ttu-id="9674f-1225">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="9674f-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9674f-1226">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="9674f-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9674f-1227">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1227">Empty</span></span> | <span data-ttu-id="9674f-1228">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9674f-1229">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9674f-1230">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="9674f-1231">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="9674f-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9674f-1232">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="9674f-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9674f-1233">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="9674f-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9674f-1234">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="9674f-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9674f-1235">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9674f-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9674f-1236">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="9674f-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9674f-1237">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9674f-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9674f-1238">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="9674f-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9674f-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9674f-1240">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="9674f-1240">JavaScript Option</span></span> | <span data-ttu-id="9674f-1241">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1241">Default Value</span></span> | <span data-ttu-id="9674f-1242">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9674f-1243">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9674f-1244">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-1245">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-1246">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9674f-1247">Java</span><span class="sxs-lookup"><span data-stu-id="9674f-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="9674f-1248">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="9674f-1248">Java Option</span></span> | <span data-ttu-id="9674f-1249">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9674f-1249">Default Value</span></span> | <span data-ttu-id="9674f-1250">Описание</span><span class="sxs-lookup"><span data-stu-id="9674f-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9674f-1251">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="9674f-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9674f-1252">Установите этот параметр `true` в значение, чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="9674f-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9674f-1253">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="9674f-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9674f-1254">Этот параметр нельзя включить при использовании службы Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9674f-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9674f-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9674f-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9674f-1256">Пусто</span><span class="sxs-lookup"><span data-stu-id="9674f-1256">Empty</span></span> | <span data-ttu-id="9674f-1257">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="9674f-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9674f-1258">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:</span><span class="sxs-lookup"><span data-stu-id="9674f-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9674f-1259">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9674f-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9674f-1260">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9674f-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9674f-1261">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9674f-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
