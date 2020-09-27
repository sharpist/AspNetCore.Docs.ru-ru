---
title: Конфигурация SignalR ASP.NET Core
author: bradygaster
description: Узнайте, как настроить ASP.NET Core SignalR приложения.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393877"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="6cd7d-103">Конфигурация SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6cd7d-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6cd7d-104">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-105">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6cd7d-106">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6cd7d-107">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6cd7d-108">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6cd7d-109">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6cd7d-110">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6cd7d-111">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6cd7d-112">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6cd7d-113">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6cd7d-114">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6cd7d-115">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6cd7d-116">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="6cd7d-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6cd7d-117">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6cd7d-118">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-118">MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-119">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6cd7d-120">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-121">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6cd7d-122">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="6cd7d-122">Configure server options</span></span>

<span data-ttu-id="6cd7d-123">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6cd7d-124">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-124">Option</span></span> | <span data-ttu-id="6cd7d-125">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-125">Default Value</span></span> | <span data-ttu-id="6cd7d-126">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6cd7d-127">30 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-127">30 seconds</span></span> | <span data-ttu-id="6cd7d-128">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="6cd7d-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6cd7d-129">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6cd7d-130">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-131">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-131">15 seconds</span></span> | <span data-ttu-id="6cd7d-132">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6cd7d-133">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-134">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-135">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-135">15 seconds</span></span> | <span data-ttu-id="6cd7d-136">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6cd7d-137">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6cd7d-138">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6cd7d-139">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-139">All installed protocols</span></span> | <span data-ttu-id="6cd7d-140">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-140">Protocols supported by this hub.</span></span> <span data-ttu-id="6cd7d-141">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6cd7d-142">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6cd7d-143">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6cd7d-144">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6cd7d-145">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6cd7d-146">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-146">32 KB</span></span> | <span data-ttu-id="6cd7d-147">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="6cd7d-148">1</span><span class="sxs-lookup"><span data-stu-id="6cd7d-148">1</span></span> | <span data-ttu-id="6cd7d-149">Максимальное число методов концентратора, которые каждый клиент может вызывать параллельно перед постановкой в очередь.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="6cd7d-150">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6cd7d-151">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6cd7d-152">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="6cd7d-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6cd7d-153">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6cd7d-154">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="6cd7d-155">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6cd7d-156">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-156">Option</span></span> | <span data-ttu-id="6cd7d-157">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-157">Default Value</span></span> | <span data-ttu-id="6cd7d-158">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6cd7d-159">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-159">32 KB</span></span> | <span data-ttu-id="6cd7d-160">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6cd7d-161">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6cd7d-162">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6cd7d-163">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6cd7d-164">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-164">32 KB</span></span> | <span data-ttu-id="6cd7d-165">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6cd7d-166">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6cd7d-167">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-167">All Transports are enabled.</span></span> | <span data-ttu-id="6cd7d-168">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6cd7d-169">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-169">See below.</span></span> | <span data-ttu-id="6cd7d-170">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6cd7d-171">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-171">See below.</span></span> | <span data-ttu-id="6cd7d-172">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="6cd7d-173">0</span><span class="sxs-lookup"><span data-stu-id="6cd7d-173">0</span></span> | <span data-ttu-id="6cd7d-174">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="6cd7d-175">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="6cd7d-176">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6cd7d-177">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-177">Option</span></span> | <span data-ttu-id="6cd7d-178">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-178">Default Value</span></span> | <span data-ttu-id="6cd7d-179">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6cd7d-180">90 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-180">90 seconds</span></span> | <span data-ttu-id="6cd7d-181">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6cd7d-182">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6cd7d-183">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6cd7d-184">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-184">Option</span></span> | <span data-ttu-id="6cd7d-185">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-185">Default Value</span></span> | <span data-ttu-id="6cd7d-186">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6cd7d-187">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-187">5 seconds</span></span> | <span data-ttu-id="6cd7d-188">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6cd7d-189">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6cd7d-190">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6cd7d-191">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="6cd7d-191">Configure client options</span></span>

<span data-ttu-id="6cd7d-192">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6cd7d-193">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6cd7d-194">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="6cd7d-194">Configure logging</span></span>

<span data-ttu-id="6cd7d-195">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6cd7d-196">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6cd7d-197">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-198">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6cd7d-199">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6cd7d-200">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6cd7d-201">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6cd7d-202">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6cd7d-203">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6cd7d-204">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6cd7d-205">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6cd7d-206">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6cd7d-207">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-207">The following table lists the available log levels.</span></span> <span data-ttu-id="6cd7d-208">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6cd7d-209">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6cd7d-210">Строка</span><span class="sxs-lookup"><span data-stu-id="6cd7d-210">String</span></span>                      | <span data-ttu-id="6cd7d-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6cd7d-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6cd7d-212">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6cd7d-213">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6cd7d-214">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6cd7d-215">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6cd7d-216">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6cd7d-217">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6cd7d-218">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6cd7d-219">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6cd7d-220">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6cd7d-221">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="6cd7d-221">Configure allowed transports</span></span>

<span data-ttu-id="6cd7d-222">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6cd7d-223">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6cd7d-224">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-224">All transports are enabled by default.</span></span>

<span data-ttu-id="6cd7d-225">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6cd7d-226">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6cd7d-227">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6cd7d-228">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6cd7d-229">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6cd7d-230">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6cd7d-231">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="6cd7d-231">Configure bearer authentication</span></span>

<span data-ttu-id="6cd7d-232">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6cd7d-233">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6cd7d-234">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6cd7d-235">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6cd7d-236">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6cd7d-237">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="6cd7d-238">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6cd7d-239">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6cd7d-240">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6cd7d-241">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="6cd7d-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6cd7d-242">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-243">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-244">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-244">Option</span></span> | <span data-ttu-id="6cd7d-245">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-245">Default value</span></span> | <span data-ttu-id="6cd7d-246">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6cd7d-247">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-248">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-248">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-249">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-250">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-251">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-252">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-252">15 seconds</span></span> | <span data-ttu-id="6cd7d-253">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-254">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-255">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-256">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-257">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-257">15 seconds</span></span> | <span data-ttu-id="6cd7d-258">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-259">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-260">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6cd7d-261">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-263">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-263">Option</span></span> | <span data-ttu-id="6cd7d-264">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-264">Default value</span></span> | <span data-ttu-id="6cd7d-265">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6cd7d-266">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-267">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-267">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-268">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6cd7d-269">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-270">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6cd7d-271">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-272">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-273">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-274">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-275">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-275">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-276">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-276">Option</span></span> | <span data-ttu-id="6cd7d-277">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-277">Default value</span></span> | <span data-ttu-id="6cd7d-278">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6cd7d-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6cd7d-280">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-281">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-281">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-282">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-283">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-284">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6cd7d-285">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-285">15 seconds</span></span> | <span data-ttu-id="6cd7d-286">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-287">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-288">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-289">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6cd7d-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6cd7d-291">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-292">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-293">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-294">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6cd7d-295">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="6cd7d-295">Configure additional options</span></span>

<span data-ttu-id="6cd7d-296">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-297">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-298">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-298">.NET Option</span></span> |  <span data-ttu-id="6cd7d-299">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-299">Default value</span></span> | <span data-ttu-id="6cd7d-300">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-301">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6cd7d-302">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-303">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-304">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6cd7d-305">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-305">Empty</span></span> | <span data-ttu-id="6cd7d-306">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6cd7d-307">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-307">Empty</span></span> | <span data-ttu-id="6cd7d-308">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6cd7d-309">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-309">Empty</span></span> | <span data-ttu-id="6cd7d-310">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6cd7d-311">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-311">5 seconds</span></span> | <span data-ttu-id="6cd7d-312">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-312">WebSockets only.</span></span> <span data-ttu-id="6cd7d-313">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6cd7d-314">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6cd7d-315">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-315">Empty</span></span> | <span data-ttu-id="6cd7d-316">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6cd7d-317">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6cd7d-318">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="6cd7d-319">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6cd7d-320">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6cd7d-321">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="6cd7d-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6cd7d-322">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6cd7d-323">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6cd7d-324">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6cd7d-325">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6cd7d-326">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-328">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-328">JavaScript Option</span></span> | <span data-ttu-id="6cd7d-329">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-329">Default Value</span></span> | <span data-ttu-id="6cd7d-330">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6cd7d-331">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="6cd7d-332">Словарь заголовков, отправленных с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="6cd7d-333">Отправка заголовков в браузере не работает для WebSockets или <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> потока.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6cd7d-334">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6cd7d-335">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-336">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-337">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="6cd7d-338">Указывает, будут ли учетные данные отправляться с запросом CORS.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="6cd7d-339">Служба приложений Azure использует cookie для прикрепленных сеансов s и требует, чтобы этот параметр был включен правильно.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="6cd7d-340">Дополнительные сведения о CORS с SignalR см. в разделе <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-341">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-341">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-342">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-342">Java Option</span></span> | <span data-ttu-id="6cd7d-343">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-343">Default Value</span></span> | <span data-ttu-id="6cd7d-344">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-345">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6cd7d-346">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-347">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-348">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6cd7d-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6cd7d-350">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-350">Empty</span></span> | <span data-ttu-id="6cd7d-351">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6cd7d-352">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6cd7d-353">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6cd7d-354">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6cd7d-355">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6cd7d-356">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-357">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6cd7d-358">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6cd7d-359">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6cd7d-360">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6cd7d-361">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6cd7d-362">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6cd7d-363">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6cd7d-364">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6cd7d-365">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6cd7d-366">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6cd7d-367">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6cd7d-368">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="6cd7d-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6cd7d-369">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6cd7d-370">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-370">MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-371">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6cd7d-372">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-373">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6cd7d-374">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="6cd7d-374">Configure server options</span></span>

<span data-ttu-id="6cd7d-375">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6cd7d-376">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-376">Option</span></span> | <span data-ttu-id="6cd7d-377">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-377">Default Value</span></span> | <span data-ttu-id="6cd7d-378">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6cd7d-379">30 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-379">30 seconds</span></span> | <span data-ttu-id="6cd7d-380">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="6cd7d-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6cd7d-381">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6cd7d-382">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-383">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-383">15 seconds</span></span> | <span data-ttu-id="6cd7d-384">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6cd7d-385">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-386">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-387">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-387">15 seconds</span></span> | <span data-ttu-id="6cd7d-388">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6cd7d-389">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6cd7d-390">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6cd7d-391">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-391">All installed protocols</span></span> | <span data-ttu-id="6cd7d-392">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-392">Protocols supported by this hub.</span></span> <span data-ttu-id="6cd7d-393">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6cd7d-394">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6cd7d-395">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6cd7d-396">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6cd7d-397">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6cd7d-398">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-398">32 KB</span></span> | <span data-ttu-id="6cd7d-399">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6cd7d-400">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6cd7d-401">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6cd7d-402">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="6cd7d-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6cd7d-403">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6cd7d-404">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="6cd7d-405">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6cd7d-406">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-406">Option</span></span> | <span data-ttu-id="6cd7d-407">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-407">Default Value</span></span> | <span data-ttu-id="6cd7d-408">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6cd7d-409">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-409">32 KB</span></span> | <span data-ttu-id="6cd7d-410">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6cd7d-411">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6cd7d-412">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6cd7d-413">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6cd7d-414">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-414">32 KB</span></span> | <span data-ttu-id="6cd7d-415">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6cd7d-416">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6cd7d-417">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-417">All Transports are enabled.</span></span> | <span data-ttu-id="6cd7d-418">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6cd7d-419">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-419">See below.</span></span> | <span data-ttu-id="6cd7d-420">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6cd7d-421">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-421">See below.</span></span> | <span data-ttu-id="6cd7d-422">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="6cd7d-423">0</span><span class="sxs-lookup"><span data-stu-id="6cd7d-423">0</span></span> | <span data-ttu-id="6cd7d-424">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="6cd7d-425">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="6cd7d-426">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6cd7d-427">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-427">Option</span></span> | <span data-ttu-id="6cd7d-428">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-428">Default Value</span></span> | <span data-ttu-id="6cd7d-429">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6cd7d-430">90 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-430">90 seconds</span></span> | <span data-ttu-id="6cd7d-431">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6cd7d-432">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6cd7d-433">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6cd7d-434">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-434">Option</span></span> | <span data-ttu-id="6cd7d-435">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-435">Default Value</span></span> | <span data-ttu-id="6cd7d-436">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6cd7d-437">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-437">5 seconds</span></span> | <span data-ttu-id="6cd7d-438">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6cd7d-439">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6cd7d-440">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6cd7d-441">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="6cd7d-441">Configure client options</span></span>

<span data-ttu-id="6cd7d-442">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6cd7d-443">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6cd7d-444">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="6cd7d-444">Configure logging</span></span>

<span data-ttu-id="6cd7d-445">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6cd7d-446">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6cd7d-447">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-448">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6cd7d-449">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6cd7d-450">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6cd7d-451">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6cd7d-452">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6cd7d-453">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6cd7d-454">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6cd7d-455">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6cd7d-456">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6cd7d-457">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-457">The following table lists the available log levels.</span></span> <span data-ttu-id="6cd7d-458">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6cd7d-459">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6cd7d-460">Строка</span><span class="sxs-lookup"><span data-stu-id="6cd7d-460">String</span></span>                      | <span data-ttu-id="6cd7d-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6cd7d-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6cd7d-462">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6cd7d-463">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6cd7d-464">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6cd7d-465">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6cd7d-466">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6cd7d-467">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6cd7d-468">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6cd7d-469">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6cd7d-470">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6cd7d-471">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="6cd7d-471">Configure allowed transports</span></span>

<span data-ttu-id="6cd7d-472">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6cd7d-473">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6cd7d-474">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-474">All transports are enabled by default.</span></span>

<span data-ttu-id="6cd7d-475">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6cd7d-476">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6cd7d-477">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6cd7d-478">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6cd7d-479">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6cd7d-480">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6cd7d-481">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="6cd7d-481">Configure bearer authentication</span></span>

<span data-ttu-id="6cd7d-482">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6cd7d-483">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6cd7d-484">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6cd7d-485">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6cd7d-486">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6cd7d-487">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="6cd7d-488">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6cd7d-489">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6cd7d-490">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6cd7d-491">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="6cd7d-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6cd7d-492">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-493">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-494">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-494">Option</span></span> | <span data-ttu-id="6cd7d-495">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-495">Default value</span></span> | <span data-ttu-id="6cd7d-496">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6cd7d-497">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-498">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-498">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-499">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-500">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-501">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-502">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-502">15 seconds</span></span> | <span data-ttu-id="6cd7d-503">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-504">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-505">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-506">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-507">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-507">15 seconds</span></span> | <span data-ttu-id="6cd7d-508">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-509">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-510">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6cd7d-511">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-513">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-513">Option</span></span> | <span data-ttu-id="6cd7d-514">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-514">Default value</span></span> | <span data-ttu-id="6cd7d-515">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6cd7d-516">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-517">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-517">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-518">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6cd7d-519">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-520">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6cd7d-521">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-522">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-523">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-524">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-525">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-525">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-526">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-526">Option</span></span> | <span data-ttu-id="6cd7d-527">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-527">Default value</span></span> | <span data-ttu-id="6cd7d-528">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6cd7d-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6cd7d-530">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-531">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-531">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-532">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-533">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-534">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6cd7d-535">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-535">15 seconds</span></span> | <span data-ttu-id="6cd7d-536">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-537">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-538">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-539">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6cd7d-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6cd7d-541">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-542">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-543">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-544">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6cd7d-545">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="6cd7d-545">Configure additional options</span></span>

<span data-ttu-id="6cd7d-546">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-547">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-548">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-548">.NET Option</span></span> |  <span data-ttu-id="6cd7d-549">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-549">Default value</span></span> | <span data-ttu-id="6cd7d-550">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-551">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6cd7d-552">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-553">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-554">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6cd7d-555">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-555">Empty</span></span> | <span data-ttu-id="6cd7d-556">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6cd7d-557">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-557">Empty</span></span> | <span data-ttu-id="6cd7d-558">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6cd7d-559">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-559">Empty</span></span> | <span data-ttu-id="6cd7d-560">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6cd7d-561">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-561">5 seconds</span></span> | <span data-ttu-id="6cd7d-562">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-562">WebSockets only.</span></span> <span data-ttu-id="6cd7d-563">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6cd7d-564">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6cd7d-565">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-565">Empty</span></span> | <span data-ttu-id="6cd7d-566">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6cd7d-567">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6cd7d-568">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="6cd7d-569">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6cd7d-570">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6cd7d-571">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="6cd7d-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6cd7d-572">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6cd7d-573">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6cd7d-574">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6cd7d-575">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6cd7d-576">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-578">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-578">JavaScript Option</span></span> | <span data-ttu-id="6cd7d-579">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-579">Default Value</span></span> | <span data-ttu-id="6cd7d-580">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6cd7d-581">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6cd7d-582">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6cd7d-583">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-584">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-585">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-586">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-586">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-587">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-587">Java Option</span></span> | <span data-ttu-id="6cd7d-588">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-588">Default Value</span></span> | <span data-ttu-id="6cd7d-589">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-590">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6cd7d-591">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-592">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-593">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6cd7d-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6cd7d-595">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-595">Empty</span></span> | <span data-ttu-id="6cd7d-596">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6cd7d-597">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6cd7d-598">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6cd7d-599">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6cd7d-600">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6cd7d-601">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-602">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6cd7d-603">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6cd7d-604">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6cd7d-605">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6cd7d-606">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6cd7d-607">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6cd7d-608">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6cd7d-609">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="6cd7d-610">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6cd7d-611">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6cd7d-612">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6cd7d-613">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="6cd7d-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6cd7d-614">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6cd7d-615">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-615">MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-616">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6cd7d-617">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-618">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6cd7d-619">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="6cd7d-619">Configure server options</span></span>

<span data-ttu-id="6cd7d-620">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6cd7d-621">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-621">Option</span></span> | <span data-ttu-id="6cd7d-622">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-622">Default Value</span></span> | <span data-ttu-id="6cd7d-623">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6cd7d-624">30 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-624">30 seconds</span></span> | <span data-ttu-id="6cd7d-625">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="6cd7d-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6cd7d-626">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6cd7d-627">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-628">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-628">15 seconds</span></span> | <span data-ttu-id="6cd7d-629">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6cd7d-630">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-631">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-632">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-632">15 seconds</span></span> | <span data-ttu-id="6cd7d-633">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6cd7d-634">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6cd7d-635">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6cd7d-636">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-636">All installed protocols</span></span> | <span data-ttu-id="6cd7d-637">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-637">Protocols supported by this hub.</span></span> <span data-ttu-id="6cd7d-638">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6cd7d-639">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6cd7d-640">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6cd7d-641">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6cd7d-642">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6cd7d-643">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-643">32 KB</span></span> | <span data-ttu-id="6cd7d-644">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6cd7d-645">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6cd7d-646">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6cd7d-647">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="6cd7d-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6cd7d-648">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6cd7d-649">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="6cd7d-650">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6cd7d-651">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-651">Option</span></span> | <span data-ttu-id="6cd7d-652">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-652">Default Value</span></span> | <span data-ttu-id="6cd7d-653">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6cd7d-654">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-654">32 KB</span></span> | <span data-ttu-id="6cd7d-655">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6cd7d-656">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6cd7d-657">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6cd7d-658">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6cd7d-659">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-659">32 KB</span></span> | <span data-ttu-id="6cd7d-660">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6cd7d-661">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6cd7d-662">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-662">All Transports are enabled.</span></span> | <span data-ttu-id="6cd7d-663">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6cd7d-664">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-664">See below.</span></span> | <span data-ttu-id="6cd7d-665">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6cd7d-666">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-666">See below.</span></span> | <span data-ttu-id="6cd7d-667">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6cd7d-668">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6cd7d-669">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-669">Option</span></span> | <span data-ttu-id="6cd7d-670">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-670">Default Value</span></span> | <span data-ttu-id="6cd7d-671">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6cd7d-672">90 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-672">90 seconds</span></span> | <span data-ttu-id="6cd7d-673">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6cd7d-674">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6cd7d-675">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6cd7d-676">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-676">Option</span></span> | <span data-ttu-id="6cd7d-677">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-677">Default Value</span></span> | <span data-ttu-id="6cd7d-678">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6cd7d-679">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-679">5 seconds</span></span> | <span data-ttu-id="6cd7d-680">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6cd7d-681">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6cd7d-682">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6cd7d-683">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="6cd7d-683">Configure client options</span></span>

<span data-ttu-id="6cd7d-684">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6cd7d-685">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6cd7d-686">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="6cd7d-686">Configure logging</span></span>

<span data-ttu-id="6cd7d-687">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6cd7d-688">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6cd7d-689">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-690">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6cd7d-691">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6cd7d-692">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6cd7d-693">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6cd7d-694">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6cd7d-695">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6cd7d-696">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6cd7d-697">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6cd7d-698">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6cd7d-699">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-699">The following table lists the available log levels.</span></span> <span data-ttu-id="6cd7d-700">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6cd7d-701">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6cd7d-702">Строка</span><span class="sxs-lookup"><span data-stu-id="6cd7d-702">String</span></span>                      | <span data-ttu-id="6cd7d-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6cd7d-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6cd7d-704">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6cd7d-705">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6cd7d-706">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6cd7d-707">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6cd7d-708">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6cd7d-709">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6cd7d-710">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6cd7d-711">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6cd7d-712">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6cd7d-713">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="6cd7d-713">Configure allowed transports</span></span>

<span data-ttu-id="6cd7d-714">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6cd7d-715">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6cd7d-716">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-716">All transports are enabled by default.</span></span>

<span data-ttu-id="6cd7d-717">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6cd7d-718">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6cd7d-719">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6cd7d-720">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6cd7d-721">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6cd7d-722">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6cd7d-723">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="6cd7d-723">Configure bearer authentication</span></span>

<span data-ttu-id="6cd7d-724">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6cd7d-725">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6cd7d-726">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6cd7d-727">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6cd7d-728">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6cd7d-729">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="6cd7d-730">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6cd7d-731">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6cd7d-732">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6cd7d-733">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="6cd7d-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6cd7d-734">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-735">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-736">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-736">Option</span></span> | <span data-ttu-id="6cd7d-737">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-737">Default value</span></span> | <span data-ttu-id="6cd7d-738">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6cd7d-739">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-740">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-740">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-741">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-742">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-743">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-744">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-744">15 seconds</span></span> | <span data-ttu-id="6cd7d-745">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-746">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-747">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-748">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-749">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-749">15 seconds</span></span> | <span data-ttu-id="6cd7d-750">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-751">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-752">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6cd7d-753">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-755">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-755">Option</span></span> | <span data-ttu-id="6cd7d-756">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-756">Default value</span></span> | <span data-ttu-id="6cd7d-757">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6cd7d-758">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-759">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-759">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-760">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6cd7d-761">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-762">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6cd7d-763">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-764">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-765">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-766">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-767">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-767">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-768">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-768">Option</span></span> | <span data-ttu-id="6cd7d-769">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-769">Default value</span></span> | <span data-ttu-id="6cd7d-770">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6cd7d-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6cd7d-772">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-773">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-773">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-774">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-775">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-776">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6cd7d-777">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-777">15 seconds</span></span> | <span data-ttu-id="6cd7d-778">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-779">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-780">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-781">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6cd7d-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6cd7d-783">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-784">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-785">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-786">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6cd7d-787">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="6cd7d-787">Configure additional options</span></span>

<span data-ttu-id="6cd7d-788">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-789">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-790">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-790">.NET Option</span></span> |  <span data-ttu-id="6cd7d-791">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-791">Default value</span></span> | <span data-ttu-id="6cd7d-792">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-793">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6cd7d-794">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-795">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-796">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6cd7d-797">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-797">Empty</span></span> | <span data-ttu-id="6cd7d-798">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6cd7d-799">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-799">Empty</span></span> | <span data-ttu-id="6cd7d-800">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6cd7d-801">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-801">Empty</span></span> | <span data-ttu-id="6cd7d-802">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6cd7d-803">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-803">5 seconds</span></span> | <span data-ttu-id="6cd7d-804">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-804">WebSockets only.</span></span> <span data-ttu-id="6cd7d-805">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6cd7d-806">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6cd7d-807">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-807">Empty</span></span> | <span data-ttu-id="6cd7d-808">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6cd7d-809">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6cd7d-810">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="6cd7d-811">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6cd7d-812">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6cd7d-813">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="6cd7d-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6cd7d-814">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6cd7d-815">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6cd7d-816">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6cd7d-817">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6cd7d-818">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-820">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-820">JavaScript Option</span></span> | <span data-ttu-id="6cd7d-821">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-821">Default Value</span></span> | <span data-ttu-id="6cd7d-822">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6cd7d-823">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6cd7d-824">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6cd7d-825">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-826">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-827">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-828">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-828">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-829">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-829">Java Option</span></span> | <span data-ttu-id="6cd7d-830">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-830">Default Value</span></span> | <span data-ttu-id="6cd7d-831">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-832">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6cd7d-833">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-834">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-835">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6cd7d-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6cd7d-837">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-837">Empty</span></span> | <span data-ttu-id="6cd7d-838">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6cd7d-839">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6cd7d-840">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6cd7d-841">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6cd7d-842">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6cd7d-843">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-844">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6cd7d-845">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6cd7d-846">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6cd7d-847">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6cd7d-848">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6cd7d-849">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6cd7d-850">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6cd7d-851">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6cd7d-852">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6cd7d-853">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6cd7d-854">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-854">MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-855">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6cd7d-856">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-857">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6cd7d-858">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="6cd7d-858">Configure server options</span></span>

<span data-ttu-id="6cd7d-859">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6cd7d-860">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-860">Option</span></span> | <span data-ttu-id="6cd7d-861">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-861">Default Value</span></span> | <span data-ttu-id="6cd7d-862">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6cd7d-863">30 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-863">30 seconds</span></span> | <span data-ttu-id="6cd7d-864">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="6cd7d-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6cd7d-865">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6cd7d-866">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-867">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-867">15 seconds</span></span> | <span data-ttu-id="6cd7d-868">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6cd7d-869">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-870">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-871">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-871">15 seconds</span></span> | <span data-ttu-id="6cd7d-872">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6cd7d-873">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6cd7d-874">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6cd7d-875">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-875">All installed protocols</span></span> | <span data-ttu-id="6cd7d-876">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-876">Protocols supported by this hub.</span></span> <span data-ttu-id="6cd7d-877">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6cd7d-878">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6cd7d-879">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6cd7d-880">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6cd7d-881">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6cd7d-882">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="6cd7d-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6cd7d-883">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6cd7d-884">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="6cd7d-885">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6cd7d-886">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-886">Option</span></span> | <span data-ttu-id="6cd7d-887">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-887">Default Value</span></span> | <span data-ttu-id="6cd7d-888">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6cd7d-889">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-889">32 KB</span></span> | <span data-ttu-id="6cd7d-890">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6cd7d-891">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6cd7d-892">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6cd7d-893">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6cd7d-894">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-894">32 KB</span></span> | <span data-ttu-id="6cd7d-895">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6cd7d-896">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6cd7d-897">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-897">All Transports are enabled.</span></span> | <span data-ttu-id="6cd7d-898">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6cd7d-899">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-899">See below.</span></span> | <span data-ttu-id="6cd7d-900">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6cd7d-901">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-901">See below.</span></span> | <span data-ttu-id="6cd7d-902">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6cd7d-903">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6cd7d-904">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-904">Option</span></span> | <span data-ttu-id="6cd7d-905">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-905">Default Value</span></span> | <span data-ttu-id="6cd7d-906">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6cd7d-907">90 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-907">90 seconds</span></span> | <span data-ttu-id="6cd7d-908">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6cd7d-909">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6cd7d-910">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6cd7d-911">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-911">Option</span></span> | <span data-ttu-id="6cd7d-912">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-912">Default Value</span></span> | <span data-ttu-id="6cd7d-913">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6cd7d-914">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-914">5 seconds</span></span> | <span data-ttu-id="6cd7d-915">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6cd7d-916">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6cd7d-917">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6cd7d-918">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="6cd7d-918">Configure client options</span></span>

<span data-ttu-id="6cd7d-919">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6cd7d-920">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6cd7d-921">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="6cd7d-921">Configure logging</span></span>

<span data-ttu-id="6cd7d-922">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6cd7d-923">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6cd7d-924">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-925">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6cd7d-926">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6cd7d-927">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6cd7d-928">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6cd7d-929">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6cd7d-930">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6cd7d-931">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6cd7d-932">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6cd7d-933">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6cd7d-934">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6cd7d-935">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6cd7d-936">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6cd7d-937">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6cd7d-938">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6cd7d-939">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="6cd7d-939">Configure allowed transports</span></span>

<span data-ttu-id="6cd7d-940">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6cd7d-941">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6cd7d-942">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-942">All transports are enabled by default.</span></span>

<span data-ttu-id="6cd7d-943">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6cd7d-944">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6cd7d-945">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6cd7d-946">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="6cd7d-946">Configure bearer authentication</span></span>

<span data-ttu-id="6cd7d-947">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6cd7d-948">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6cd7d-949">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6cd7d-950">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6cd7d-951">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6cd7d-952">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="6cd7d-953">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6cd7d-954">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6cd7d-955">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6cd7d-956">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="6cd7d-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6cd7d-957">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-958">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-959">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-959">Option</span></span> | <span data-ttu-id="6cd7d-960">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-960">Default value</span></span> | <span data-ttu-id="6cd7d-961">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6cd7d-962">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-963">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-963">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-964">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-965">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-966">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-967">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-967">15 seconds</span></span> | <span data-ttu-id="6cd7d-968">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-969">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-970">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-971">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-972">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-972">15 seconds</span></span> | <span data-ttu-id="6cd7d-973">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-974">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-975">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6cd7d-976">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-978">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-978">Option</span></span> | <span data-ttu-id="6cd7d-979">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-979">Default value</span></span> | <span data-ttu-id="6cd7d-980">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6cd7d-981">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-982">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-982">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-983">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6cd7d-984">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-985">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6cd7d-986">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-987">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-988">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-989">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-990">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-990">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-991">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-991">Option</span></span> | <span data-ttu-id="6cd7d-992">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-992">Default value</span></span> | <span data-ttu-id="6cd7d-993">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6cd7d-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6cd7d-995">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-996">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-996">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-997">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-998">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-999">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6cd7d-1000">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1000">15 seconds</span></span> | <span data-ttu-id="6cd7d-1001">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-1002">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-1003">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-1004">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6cd7d-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6cd7d-1006">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-1007">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6cd7d-1008">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6cd7d-1009">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6cd7d-1010">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1010">Configure additional options</span></span>

<span data-ttu-id="6cd7d-1011">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-1013">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1013">.NET Option</span></span> |  <span data-ttu-id="6cd7d-1014">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1014">Default value</span></span> | <span data-ttu-id="6cd7d-1015">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-1016">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6cd7d-1017">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-1018">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-1019">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6cd7d-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1020">Empty</span></span> | <span data-ttu-id="6cd7d-1021">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6cd7d-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1022">Empty</span></span> | <span data-ttu-id="6cd7d-1023">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6cd7d-1024">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1024">Empty</span></span> | <span data-ttu-id="6cd7d-1025">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6cd7d-1026">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1026">5 seconds</span></span> | <span data-ttu-id="6cd7d-1027">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1027">WebSockets only.</span></span> <span data-ttu-id="6cd7d-1028">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6cd7d-1029">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6cd7d-1030">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1030">Empty</span></span> | <span data-ttu-id="6cd7d-1031">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6cd7d-1032">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6cd7d-1033">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="6cd7d-1034">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6cd7d-1035">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6cd7d-1036">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6cd7d-1037">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6cd7d-1038">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6cd7d-1039">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6cd7d-1040">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6cd7d-1041">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-1043">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1043">JavaScript Option</span></span> | <span data-ttu-id="6cd7d-1044">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1044">Default Value</span></span> | <span data-ttu-id="6cd7d-1045">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6cd7d-1046">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6cd7d-1047">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6cd7d-1048">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-1049">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-1050">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-1051">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-1052">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1052">Java Option</span></span> | <span data-ttu-id="6cd7d-1053">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1053">Default Value</span></span> | <span data-ttu-id="6cd7d-1054">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-1055">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6cd7d-1056">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-1057">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-1058">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6cd7d-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6cd7d-1060">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1060">Empty</span></span> | <span data-ttu-id="6cd7d-1061">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6cd7d-1062">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6cd7d-1063">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6cd7d-1064">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6cd7d-1065">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6cd7d-1066">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-1067">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6cd7d-1068">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6cd7d-1069">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6cd7d-1070">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6cd7d-1071">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6cd7d-1072">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6cd7d-1073">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6cd7d-1074">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6cd7d-1075">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6cd7d-1076">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6cd7d-1077">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1077">MessagePack serialization options</span></span>

<span data-ttu-id="6cd7d-1078">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6cd7d-1079">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-1080">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6cd7d-1081">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1081">Configure server options</span></span>

<span data-ttu-id="6cd7d-1082">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6cd7d-1083">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1083">Option</span></span> | <span data-ttu-id="6cd7d-1084">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1084">Default Value</span></span> | <span data-ttu-id="6cd7d-1085">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-1086">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1086">15 seconds</span></span> | <span data-ttu-id="6cd7d-1087">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6cd7d-1088">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-1089">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6cd7d-1090">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1090">15 seconds</span></span> | <span data-ttu-id="6cd7d-1091">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6cd7d-1092">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6cd7d-1093">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6cd7d-1094">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1094">All installed protocols</span></span> | <span data-ttu-id="6cd7d-1095">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="6cd7d-1096">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6cd7d-1097">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6cd7d-1098">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6cd7d-1099">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6cd7d-1100">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6cd7d-1101">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6cd7d-1102">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6cd7d-1103">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="6cd7d-1104">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6cd7d-1105">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1105">Option</span></span> | <span data-ttu-id="6cd7d-1106">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1106">Default Value</span></span> | <span data-ttu-id="6cd7d-1107">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6cd7d-1108">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1108">32 KB</span></span> | <span data-ttu-id="6cd7d-1109">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6cd7d-1110">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6cd7d-1111">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6cd7d-1112">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6cd7d-1113">32 КБ</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1113">32 KB</span></span> | <span data-ttu-id="6cd7d-1114">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6cd7d-1115">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6cd7d-1116">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1116">All Transports are enabled.</span></span> | <span data-ttu-id="6cd7d-1117">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6cd7d-1118">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1118">See below.</span></span> | <span data-ttu-id="6cd7d-1119">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6cd7d-1120">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1120">See below.</span></span> | <span data-ttu-id="6cd7d-1121">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6cd7d-1122">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6cd7d-1123">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1123">Option</span></span> | <span data-ttu-id="6cd7d-1124">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1124">Default Value</span></span> | <span data-ttu-id="6cd7d-1125">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6cd7d-1126">90 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1126">90 seconds</span></span> | <span data-ttu-id="6cd7d-1127">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6cd7d-1128">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6cd7d-1129">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6cd7d-1130">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1130">Option</span></span> | <span data-ttu-id="6cd7d-1131">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1131">Default Value</span></span> | <span data-ttu-id="6cd7d-1132">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6cd7d-1133">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1133">5 seconds</span></span> | <span data-ttu-id="6cd7d-1134">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6cd7d-1135">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6cd7d-1136">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6cd7d-1137">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1137">Configure client options</span></span>

<span data-ttu-id="6cd7d-1138">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6cd7d-1139">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6cd7d-1140">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1140">Configure logging</span></span>

<span data-ttu-id="6cd7d-1141">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6cd7d-1142">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6cd7d-1143">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd7d-1144">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6cd7d-1145">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6cd7d-1146">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6cd7d-1147">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6cd7d-1148">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6cd7d-1149">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6cd7d-1150">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6cd7d-1151">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6cd7d-1152">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6cd7d-1153">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6cd7d-1154">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6cd7d-1155">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6cd7d-1156">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6cd7d-1157">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6cd7d-1158">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1158">Configure allowed transports</span></span>

<span data-ttu-id="6cd7d-1159">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6cd7d-1160">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6cd7d-1161">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="6cd7d-1162">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6cd7d-1163">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6cd7d-1164">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1164">Configure bearer authentication</span></span>

<span data-ttu-id="6cd7d-1165">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6cd7d-1166">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6cd7d-1167">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6cd7d-1168">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6cd7d-1169">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6cd7d-1170">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="6cd7d-1171">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6cd7d-1172">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6cd7d-1173">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6cd7d-1174">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6cd7d-1175">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-1177">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1177">Option</span></span> | <span data-ttu-id="6cd7d-1178">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1178">Default value</span></span> | <span data-ttu-id="6cd7d-1179">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6cd7d-1180">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-1181">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1181">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-1182">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-1183">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-1184">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6cd7d-1185">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1185">15 seconds</span></span> | <span data-ttu-id="6cd7d-1186">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-1187">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6cd7d-1188">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-1189">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="6cd7d-1190">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-1192">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1192">Option</span></span> | <span data-ttu-id="6cd7d-1193">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1193">Default value</span></span> | <span data-ttu-id="6cd7d-1194">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6cd7d-1195">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-1196">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1196">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-1197">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6cd7d-1198">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-1199">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-1200">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-1201">Параметр</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1201">Option</span></span> | <span data-ttu-id="6cd7d-1202">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1202">Default value</span></span> | <span data-ttu-id="6cd7d-1203">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6cd7d-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6cd7d-1205">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6cd7d-1206">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1206">Timeout for server activity.</span></span> <span data-ttu-id="6cd7d-1207">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-1208">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6cd7d-1209">Рекомендуемое значение — это число, по крайней мере, двойное `KeepAliveInterval` значение сервера, которое позволяет получить время для проверки связи.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6cd7d-1210">15 секунд</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1210">15 seconds</span></span> | <span data-ttu-id="6cd7d-1211">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="6cd7d-1212">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6cd7d-1213">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6cd7d-1214">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6cd7d-1215">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1215">Configure additional options</span></span>

<span data-ttu-id="6cd7d-1216">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6cd7d-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6cd7d-1218">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1218">.NET Option</span></span> |  <span data-ttu-id="6cd7d-1219">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1219">Default value</span></span> | <span data-ttu-id="6cd7d-1220">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-1221">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6cd7d-1222">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-1223">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-1224">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6cd7d-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1225">Empty</span></span> | <span data-ttu-id="6cd7d-1226">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6cd7d-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1227">Empty</span></span> | <span data-ttu-id="6cd7d-1228">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6cd7d-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1229">Empty</span></span> | <span data-ttu-id="6cd7d-1230">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6cd7d-1231">5 с</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1231">5 seconds</span></span> | <span data-ttu-id="6cd7d-1232">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1232">WebSockets only.</span></span> <span data-ttu-id="6cd7d-1233">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6cd7d-1234">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6cd7d-1235">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1235">Empty</span></span> | <span data-ttu-id="6cd7d-1236">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6cd7d-1237">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6cd7d-1238">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="6cd7d-1239">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6cd7d-1240">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6cd7d-1241">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6cd7d-1242">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6cd7d-1243">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6cd7d-1244">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6cd7d-1245">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6cd7d-1246">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6cd7d-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6cd7d-1248">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1248">JavaScript Option</span></span> | <span data-ttu-id="6cd7d-1249">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1249">Default Value</span></span> | <span data-ttu-id="6cd7d-1250">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6cd7d-1251">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6cd7d-1252">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6cd7d-1253">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-1254">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-1255">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6cd7d-1256">Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="6cd7d-1257">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1257">Java Option</span></span> | <span data-ttu-id="6cd7d-1258">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1258">Default Value</span></span> | <span data-ttu-id="6cd7d-1259">Описание</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6cd7d-1260">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6cd7d-1261">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6cd7d-1262">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6cd7d-1263">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6cd7d-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6cd7d-1265">Empty</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1265">Empty</span></span> | <span data-ttu-id="6cd7d-1266">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6cd7d-1267">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6cd7d-1268">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6cd7d-1269">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6cd7d-1270">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6cd7d-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
