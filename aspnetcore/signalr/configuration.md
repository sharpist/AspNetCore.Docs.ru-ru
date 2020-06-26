---
title: Конфигурация SignalR ASP.NET Core
author: bradygaster
description: Узнайте, как настроить ASP.NET Core SignalR приложения.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c711c2163908e3fdd20e3bb497f333ebd495d921
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406840"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="047de-103">Конфигурация SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="047de-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="047de-104">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="047de-105">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="047de-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="047de-106">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="047de-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="047de-107">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="047de-108">`AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="047de-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="047de-109">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="047de-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="047de-110">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="047de-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="047de-111">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="047de-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="047de-112">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="047de-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="047de-113">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="047de-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="047de-114">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="047de-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="047de-115">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="047de-116">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="047de-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="047de-117">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="047de-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="047de-118">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-118">MessagePack serialization options</span></span>

<span data-ttu-id="047de-119">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="047de-120">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-121">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="047de-122">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="047de-122">Configure server options</span></span>

<span data-ttu-id="047de-123">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="047de-124">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-124">Option</span></span> | <span data-ttu-id="047de-125">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-125">Default Value</span></span> | <span data-ttu-id="047de-126">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="047de-127">30 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-127">30 seconds</span></span> | <span data-ttu-id="047de-128">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="047de-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="047de-129">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="047de-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="047de-130">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="047de-131">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-131">15 seconds</span></span> | <span data-ttu-id="047de-132">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="047de-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="047de-133">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-134">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-135">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-135">15 seconds</span></span> | <span data-ttu-id="047de-136">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="047de-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="047de-137">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="047de-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="047de-138">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="047de-139">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="047de-139">All installed protocols</span></span> | <span data-ttu-id="047de-140">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="047de-140">Protocols supported by this hub.</span></span> <span data-ttu-id="047de-141">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="047de-142">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="047de-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="047de-143">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="047de-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="047de-144">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="047de-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="047de-145">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="047de-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="047de-146">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-146">32 KB</span></span> | <span data-ttu-id="047de-147">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="047de-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="047de-148">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="047de-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="047de-149">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="047de-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="047de-150">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="047de-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="047de-151">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="047de-152">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="047de-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="047de-153">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="047de-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="047de-154">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-154">Option</span></span> | <span data-ttu-id="047de-155">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-155">Default Value</span></span> | <span data-ttu-id="047de-156">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="047de-157">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-157">32 KB</span></span> | <span data-ttu-id="047de-158">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="047de-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="047de-159">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="047de-160">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="047de-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="047de-161">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="047de-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="047de-162">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-162">32 KB</span></span> | <span data-ttu-id="047de-163">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="047de-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="047de-164">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="047de-165">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-165">All Transports are enabled.</span></span> | <span data-ttu-id="047de-166">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="047de-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="047de-167">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-167">See below.</span></span> | <span data-ttu-id="047de-168">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="047de-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="047de-169">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-169">See below.</span></span> | <span data-ttu-id="047de-170">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="047de-171">0</span><span class="sxs-lookup"><span data-stu-id="047de-171">0</span></span> | <span data-ttu-id="047de-172">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="047de-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="047de-173">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="047de-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="047de-174">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="047de-175">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-175">Option</span></span> | <span data-ttu-id="047de-176">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-176">Default Value</span></span> | <span data-ttu-id="047de-177">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="047de-178">90 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-178">90 seconds</span></span> | <span data-ttu-id="047de-179">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="047de-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="047de-180">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="047de-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="047de-181">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="047de-182">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-182">Option</span></span> | <span data-ttu-id="047de-183">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-183">Default Value</span></span> | <span data-ttu-id="047de-184">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="047de-185">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-185">5 seconds</span></span> | <span data-ttu-id="047de-186">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="047de-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="047de-187">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="047de-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="047de-188">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="047de-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="047de-189">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="047de-189">Configure client options</span></span>

<span data-ttu-id="047de-190">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="047de-191">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="047de-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="047de-192">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="047de-192">Configure logging</span></span>

<span data-ttu-id="047de-193">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="047de-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="047de-194">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="047de-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="047de-195">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="047de-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-196">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="047de-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="047de-197">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="047de-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="047de-198">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="047de-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="047de-199">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="047de-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="047de-200">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="047de-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="047de-201">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="047de-202">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="047de-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="047de-203">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="047de-204">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="047de-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="047de-205">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-205">The following table lists the available log levels.</span></span> <span data-ttu-id="047de-206">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="047de-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="047de-207">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="047de-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="047de-208">Строка</span><span class="sxs-lookup"><span data-stu-id="047de-208">String</span></span>                      | <span data-ttu-id="047de-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="047de-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="047de-210">`info` **или** `information`</span><span class="sxs-lookup"><span data-stu-id="047de-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="047de-211">`warn` **или** `warning`</span><span class="sxs-lookup"><span data-stu-id="047de-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="047de-212">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="047de-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="047de-213">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="047de-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="047de-214">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="047de-215">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="047de-216">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="047de-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="047de-217">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="047de-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="047de-218">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="047de-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="047de-219">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="047de-219">Configure allowed transports</span></span>

<span data-ttu-id="047de-220">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="047de-221">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="047de-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="047de-222">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-222">All transports are enabled by default.</span></span>

<span data-ttu-id="047de-223">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="047de-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="047de-224">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="047de-225">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="047de-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="047de-226">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="047de-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="047de-227">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="047de-228">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="047de-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="047de-229">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="047de-229">Configure bearer authentication</span></span>

<span data-ttu-id="047de-230">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="047de-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="047de-231">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="047de-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="047de-232">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="047de-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="047de-233">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="047de-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="047de-234">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="047de-235">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="047de-236">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="047de-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="047de-237">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="047de-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="047de-238">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="047de-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="047de-239">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="047de-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="047de-240">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="047de-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-241">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-242">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-242">Option</span></span> | <span data-ttu-id="047de-243">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-243">Default value</span></span> | <span data-ttu-id="047de-244">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="047de-245">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-246">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-246">Timeout for server activity.</span></span> <span data-ttu-id="047de-247">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-248">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-249">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="047de-250">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-250">15 seconds</span></span> | <span data-ttu-id="047de-251">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-252">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-253">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-254">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-255">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-255">15 seconds</span></span> | <span data-ttu-id="047de-256">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-257">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-258">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="047de-259">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="047de-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="047de-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-261">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-261">Option</span></span> | <span data-ttu-id="047de-262">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-262">Default value</span></span> | <span data-ttu-id="047de-263">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="047de-264">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-265">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-265">Timeout for server activity.</span></span> <span data-ttu-id="047de-266">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="047de-267">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-268">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="047de-269">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="047de-270">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-271">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-272">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-273">Java</span><span class="sxs-lookup"><span data-stu-id="047de-273">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-274">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-274">Option</span></span> | <span data-ttu-id="047de-275">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-275">Default value</span></span> | <span data-ttu-id="047de-276">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="047de-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="047de-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="047de-278">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-279">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-279">Timeout for server activity.</span></span> <span data-ttu-id="047de-280">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-281">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-282">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="047de-283">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-283">15 seconds</span></span> | <span data-ttu-id="047de-284">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-285">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-286">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-287">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="047de-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="047de-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="047de-289">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="047de-290">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-291">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-292">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="047de-293">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="047de-293">Configure additional options</span></span>

<span data-ttu-id="047de-294">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="047de-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-295">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-296">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="047de-296">.NET Option</span></span> |  <span data-ttu-id="047de-297">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-297">Default value</span></span> | <span data-ttu-id="047de-298">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="047de-299">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="047de-300">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-301">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-302">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="047de-303">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-303">Empty</span></span> | <span data-ttu-id="047de-304">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="047de-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="047de-305">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-305">Empty</span></span> | <span data-ttu-id="047de-306">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="047de-307">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-307">Empty</span></span> | <span data-ttu-id="047de-308">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="047de-309">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-309">5 seconds</span></span> | <span data-ttu-id="047de-310">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-310">WebSockets only.</span></span> <span data-ttu-id="047de-311">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="047de-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="047de-312">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="047de-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="047de-313">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-313">Empty</span></span> | <span data-ttu-id="047de-314">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="047de-315">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="047de-316">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="047de-317">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="047de-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="047de-318">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="047de-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="047de-319">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="047de-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="047de-320">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="047de-321">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="047de-322">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="047de-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="047de-323">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="047de-324">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="047de-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="047de-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-326">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-326">JavaScript Option</span></span> | <span data-ttu-id="047de-327">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-327">Default Value</span></span> | <span data-ttu-id="047de-328">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="047de-329">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="047de-330">Словарь заголовков, отправленных с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="047de-331">Отправка заголовков в браузере не работает для WebSockets или <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> потока.</span><span class="sxs-lookup"><span data-stu-id="047de-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="047de-332">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="047de-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="047de-333">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-334">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-335">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="047de-336">Указывает, будут ли учетные данные отправляться с запросом CORS.</span><span class="sxs-lookup"><span data-stu-id="047de-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="047de-337">Служба приложений Azure использует файлы cookie для закрепленных сеансов и требует, чтобы этот параметр был включен правильно.</span><span class="sxs-lookup"><span data-stu-id="047de-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="047de-338">Дополнительные сведения о CORS с SignalR см. в разделе <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="047de-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-339">Java</span><span class="sxs-lookup"><span data-stu-id="047de-339">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-340">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="047de-340">Java Option</span></span> | <span data-ttu-id="047de-341">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-341">Default Value</span></span> | <span data-ttu-id="047de-342">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="047de-343">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="047de-344">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-345">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-346">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="047de-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="047de-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="047de-348">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-348">Empty</span></span> | <span data-ttu-id="047de-349">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="047de-350">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="047de-351">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="047de-352">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="047de-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="047de-353">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="047de-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="047de-354">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="047de-355">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="047de-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="047de-356">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="047de-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="047de-357">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="047de-358">`AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="047de-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="047de-359">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="047de-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="047de-360">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="047de-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="047de-361">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="047de-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="047de-362">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="047de-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="047de-363">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="047de-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="047de-364">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="047de-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="047de-365">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="047de-366">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="047de-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="047de-367">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="047de-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="047de-368">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-368">MessagePack serialization options</span></span>

<span data-ttu-id="047de-369">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="047de-370">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-371">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="047de-372">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="047de-372">Configure server options</span></span>

<span data-ttu-id="047de-373">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="047de-374">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-374">Option</span></span> | <span data-ttu-id="047de-375">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-375">Default Value</span></span> | <span data-ttu-id="047de-376">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="047de-377">30 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-377">30 seconds</span></span> | <span data-ttu-id="047de-378">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="047de-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="047de-379">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="047de-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="047de-380">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="047de-381">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-381">15 seconds</span></span> | <span data-ttu-id="047de-382">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="047de-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="047de-383">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-384">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-385">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-385">15 seconds</span></span> | <span data-ttu-id="047de-386">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="047de-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="047de-387">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="047de-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="047de-388">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="047de-389">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="047de-389">All installed protocols</span></span> | <span data-ttu-id="047de-390">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="047de-390">Protocols supported by this hub.</span></span> <span data-ttu-id="047de-391">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="047de-392">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="047de-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="047de-393">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="047de-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="047de-394">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="047de-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="047de-395">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="047de-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="047de-396">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-396">32 KB</span></span> | <span data-ttu-id="047de-397">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="047de-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="047de-398">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="047de-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="047de-399">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="047de-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="047de-400">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="047de-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="047de-401">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="047de-402">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="047de-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="047de-403">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="047de-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="047de-404">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-404">Option</span></span> | <span data-ttu-id="047de-405">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-405">Default Value</span></span> | <span data-ttu-id="047de-406">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="047de-407">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-407">32 KB</span></span> | <span data-ttu-id="047de-408">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="047de-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="047de-409">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="047de-410">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="047de-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="047de-411">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="047de-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="047de-412">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-412">32 KB</span></span> | <span data-ttu-id="047de-413">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="047de-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="047de-414">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="047de-415">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-415">All Transports are enabled.</span></span> | <span data-ttu-id="047de-416">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="047de-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="047de-417">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-417">See below.</span></span> | <span data-ttu-id="047de-418">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="047de-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="047de-419">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-419">See below.</span></span> | <span data-ttu-id="047de-420">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="047de-421">0</span><span class="sxs-lookup"><span data-stu-id="047de-421">0</span></span> | <span data-ttu-id="047de-422">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="047de-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="047de-423">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="047de-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="047de-424">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="047de-425">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-425">Option</span></span> | <span data-ttu-id="047de-426">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-426">Default Value</span></span> | <span data-ttu-id="047de-427">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="047de-428">90 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-428">90 seconds</span></span> | <span data-ttu-id="047de-429">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="047de-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="047de-430">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="047de-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="047de-431">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="047de-432">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-432">Option</span></span> | <span data-ttu-id="047de-433">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-433">Default Value</span></span> | <span data-ttu-id="047de-434">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="047de-435">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-435">5 seconds</span></span> | <span data-ttu-id="047de-436">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="047de-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="047de-437">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="047de-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="047de-438">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="047de-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="047de-439">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="047de-439">Configure client options</span></span>

<span data-ttu-id="047de-440">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="047de-441">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="047de-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="047de-442">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="047de-442">Configure logging</span></span>

<span data-ttu-id="047de-443">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="047de-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="047de-444">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="047de-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="047de-445">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="047de-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-446">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="047de-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="047de-447">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="047de-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="047de-448">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="047de-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="047de-449">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="047de-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="047de-450">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="047de-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="047de-451">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="047de-452">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="047de-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="047de-453">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="047de-454">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="047de-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="047de-455">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-455">The following table lists the available log levels.</span></span> <span data-ttu-id="047de-456">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="047de-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="047de-457">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="047de-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="047de-458">Строка</span><span class="sxs-lookup"><span data-stu-id="047de-458">String</span></span>                      | <span data-ttu-id="047de-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="047de-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="047de-460">`info` **или** `information`</span><span class="sxs-lookup"><span data-stu-id="047de-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="047de-461">`warn` **или** `warning`</span><span class="sxs-lookup"><span data-stu-id="047de-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="047de-462">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="047de-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="047de-463">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="047de-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="047de-464">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="047de-465">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="047de-466">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="047de-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="047de-467">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="047de-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="047de-468">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="047de-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="047de-469">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="047de-469">Configure allowed transports</span></span>

<span data-ttu-id="047de-470">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="047de-471">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="047de-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="047de-472">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-472">All transports are enabled by default.</span></span>

<span data-ttu-id="047de-473">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="047de-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="047de-474">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="047de-475">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="047de-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="047de-476">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="047de-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="047de-477">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="047de-478">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="047de-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="047de-479">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="047de-479">Configure bearer authentication</span></span>

<span data-ttu-id="047de-480">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="047de-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="047de-481">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="047de-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="047de-482">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="047de-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="047de-483">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="047de-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="047de-484">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="047de-485">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="047de-486">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="047de-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="047de-487">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="047de-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="047de-488">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="047de-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="047de-489">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="047de-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="047de-490">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="047de-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-491">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-492">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-492">Option</span></span> | <span data-ttu-id="047de-493">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-493">Default value</span></span> | <span data-ttu-id="047de-494">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="047de-495">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-496">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-496">Timeout for server activity.</span></span> <span data-ttu-id="047de-497">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-498">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-499">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="047de-500">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-500">15 seconds</span></span> | <span data-ttu-id="047de-501">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-502">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-503">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-504">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-505">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-505">15 seconds</span></span> | <span data-ttu-id="047de-506">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-507">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-508">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="047de-509">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="047de-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="047de-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-511">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-511">Option</span></span> | <span data-ttu-id="047de-512">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-512">Default value</span></span> | <span data-ttu-id="047de-513">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="047de-514">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-515">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-515">Timeout for server activity.</span></span> <span data-ttu-id="047de-516">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="047de-517">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-518">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="047de-519">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="047de-520">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-521">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-522">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-523">Java</span><span class="sxs-lookup"><span data-stu-id="047de-523">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-524">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-524">Option</span></span> | <span data-ttu-id="047de-525">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-525">Default value</span></span> | <span data-ttu-id="047de-526">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="047de-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="047de-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="047de-528">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-529">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-529">Timeout for server activity.</span></span> <span data-ttu-id="047de-530">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-531">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-532">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="047de-533">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-533">15 seconds</span></span> | <span data-ttu-id="047de-534">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-535">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-536">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-537">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="047de-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="047de-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="047de-539">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="047de-540">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-541">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-542">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="047de-543">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="047de-543">Configure additional options</span></span>

<span data-ttu-id="047de-544">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="047de-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-545">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-546">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="047de-546">.NET Option</span></span> |  <span data-ttu-id="047de-547">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-547">Default value</span></span> | <span data-ttu-id="047de-548">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="047de-549">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="047de-550">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-551">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-552">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="047de-553">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-553">Empty</span></span> | <span data-ttu-id="047de-554">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="047de-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="047de-555">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-555">Empty</span></span> | <span data-ttu-id="047de-556">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="047de-557">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-557">Empty</span></span> | <span data-ttu-id="047de-558">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="047de-559">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-559">5 seconds</span></span> | <span data-ttu-id="047de-560">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-560">WebSockets only.</span></span> <span data-ttu-id="047de-561">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="047de-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="047de-562">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="047de-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="047de-563">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-563">Empty</span></span> | <span data-ttu-id="047de-564">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="047de-565">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="047de-566">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="047de-567">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="047de-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="047de-568">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="047de-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="047de-569">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="047de-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="047de-570">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="047de-571">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="047de-572">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="047de-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="047de-573">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="047de-574">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="047de-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="047de-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-576">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-576">JavaScript Option</span></span> | <span data-ttu-id="047de-577">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-577">Default Value</span></span> | <span data-ttu-id="047de-578">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="047de-579">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="047de-580">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="047de-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="047de-581">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-582">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-583">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-584">Java</span><span class="sxs-lookup"><span data-stu-id="047de-584">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-585">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="047de-585">Java Option</span></span> | <span data-ttu-id="047de-586">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-586">Default Value</span></span> | <span data-ttu-id="047de-587">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="047de-588">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="047de-589">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-590">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-591">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="047de-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="047de-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="047de-593">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-593">Empty</span></span> | <span data-ttu-id="047de-594">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="047de-595">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="047de-596">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="047de-597">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="047de-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="047de-598">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="047de-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="047de-599">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="047de-600">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="047de-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="047de-601">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="047de-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="047de-602">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="047de-603">`AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="047de-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="047de-604">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="047de-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="047de-605">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="047de-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="047de-606">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="047de-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="047de-607">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="047de-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="047de-608">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="047de-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="047de-609">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="047de-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="047de-610">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="047de-611">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="047de-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="047de-612">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="047de-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="047de-613">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-613">MessagePack serialization options</span></span>

<span data-ttu-id="047de-614">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="047de-615">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-616">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="047de-617">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="047de-617">Configure server options</span></span>

<span data-ttu-id="047de-618">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="047de-619">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-619">Option</span></span> | <span data-ttu-id="047de-620">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-620">Default Value</span></span> | <span data-ttu-id="047de-621">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="047de-622">30 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-622">30 seconds</span></span> | <span data-ttu-id="047de-623">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="047de-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="047de-624">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="047de-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="047de-625">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="047de-626">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-626">15 seconds</span></span> | <span data-ttu-id="047de-627">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="047de-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="047de-628">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-629">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-630">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-630">15 seconds</span></span> | <span data-ttu-id="047de-631">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="047de-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="047de-632">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="047de-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="047de-633">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="047de-634">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="047de-634">All installed protocols</span></span> | <span data-ttu-id="047de-635">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="047de-635">Protocols supported by this hub.</span></span> <span data-ttu-id="047de-636">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="047de-637">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="047de-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="047de-638">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="047de-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="047de-639">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="047de-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="047de-640">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="047de-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="047de-641">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-641">32 KB</span></span> | <span data-ttu-id="047de-642">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="047de-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="047de-643">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="047de-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="047de-644">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="047de-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="047de-645">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="047de-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="047de-646">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="047de-647">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="047de-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="047de-648">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="047de-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="047de-649">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-649">Option</span></span> | <span data-ttu-id="047de-650">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-650">Default Value</span></span> | <span data-ttu-id="047de-651">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="047de-652">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-652">32 KB</span></span> | <span data-ttu-id="047de-653">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="047de-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="047de-654">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="047de-655">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="047de-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="047de-656">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="047de-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="047de-657">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-657">32 KB</span></span> | <span data-ttu-id="047de-658">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="047de-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="047de-659">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="047de-660">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-660">All Transports are enabled.</span></span> | <span data-ttu-id="047de-661">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="047de-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="047de-662">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-662">See below.</span></span> | <span data-ttu-id="047de-663">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="047de-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="047de-664">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-664">See below.</span></span> | <span data-ttu-id="047de-665">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="047de-666">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="047de-667">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-667">Option</span></span> | <span data-ttu-id="047de-668">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-668">Default Value</span></span> | <span data-ttu-id="047de-669">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="047de-670">90 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-670">90 seconds</span></span> | <span data-ttu-id="047de-671">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="047de-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="047de-672">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="047de-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="047de-673">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="047de-674">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-674">Option</span></span> | <span data-ttu-id="047de-675">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-675">Default Value</span></span> | <span data-ttu-id="047de-676">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="047de-677">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-677">5 seconds</span></span> | <span data-ttu-id="047de-678">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="047de-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="047de-679">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="047de-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="047de-680">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="047de-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="047de-681">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="047de-681">Configure client options</span></span>

<span data-ttu-id="047de-682">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="047de-683">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="047de-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="047de-684">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="047de-684">Configure logging</span></span>

<span data-ttu-id="047de-685">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="047de-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="047de-686">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="047de-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="047de-687">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="047de-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-688">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="047de-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="047de-689">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="047de-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="047de-690">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="047de-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="047de-691">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="047de-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="047de-692">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="047de-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="047de-693">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="047de-694">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="047de-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="047de-695">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="047de-696">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="047de-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="047de-697">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-697">The following table lists the available log levels.</span></span> <span data-ttu-id="047de-698">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="047de-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="047de-699">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="047de-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="047de-700">Строка</span><span class="sxs-lookup"><span data-stu-id="047de-700">String</span></span>                      | <span data-ttu-id="047de-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="047de-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="047de-702">`info` **или** `information`</span><span class="sxs-lookup"><span data-stu-id="047de-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="047de-703">`warn` **или** `warning`</span><span class="sxs-lookup"><span data-stu-id="047de-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="047de-704">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="047de-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="047de-705">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="047de-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="047de-706">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="047de-707">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="047de-708">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="047de-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="047de-709">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="047de-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="047de-710">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="047de-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="047de-711">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="047de-711">Configure allowed transports</span></span>

<span data-ttu-id="047de-712">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="047de-713">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="047de-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="047de-714">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-714">All transports are enabled by default.</span></span>

<span data-ttu-id="047de-715">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="047de-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="047de-716">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="047de-717">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="047de-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="047de-718">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="047de-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="047de-719">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="047de-720">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="047de-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="047de-721">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="047de-721">Configure bearer authentication</span></span>

<span data-ttu-id="047de-722">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="047de-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="047de-723">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="047de-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="047de-724">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="047de-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="047de-725">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="047de-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="047de-726">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="047de-727">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="047de-728">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="047de-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="047de-729">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="047de-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="047de-730">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="047de-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="047de-731">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="047de-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="047de-732">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="047de-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-733">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-734">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-734">Option</span></span> | <span data-ttu-id="047de-735">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-735">Default value</span></span> | <span data-ttu-id="047de-736">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="047de-737">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-738">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-738">Timeout for server activity.</span></span> <span data-ttu-id="047de-739">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-740">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-741">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="047de-742">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-742">15 seconds</span></span> | <span data-ttu-id="047de-743">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-744">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-745">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-746">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-747">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-747">15 seconds</span></span> | <span data-ttu-id="047de-748">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-749">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-750">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="047de-751">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="047de-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="047de-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-753">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-753">Option</span></span> | <span data-ttu-id="047de-754">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-754">Default value</span></span> | <span data-ttu-id="047de-755">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="047de-756">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-757">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-757">Timeout for server activity.</span></span> <span data-ttu-id="047de-758">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="047de-759">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-760">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="047de-761">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="047de-762">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-763">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-764">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-765">Java</span><span class="sxs-lookup"><span data-stu-id="047de-765">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-766">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-766">Option</span></span> | <span data-ttu-id="047de-767">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-767">Default value</span></span> | <span data-ttu-id="047de-768">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="047de-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="047de-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="047de-770">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-771">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-771">Timeout for server activity.</span></span> <span data-ttu-id="047de-772">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-773">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-774">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="047de-775">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-775">15 seconds</span></span> | <span data-ttu-id="047de-776">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-777">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-778">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-779">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="047de-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="047de-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="047de-781">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="047de-782">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-783">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-784">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="047de-785">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="047de-785">Configure additional options</span></span>

<span data-ttu-id="047de-786">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="047de-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-787">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-788">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="047de-788">.NET Option</span></span> |  <span data-ttu-id="047de-789">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-789">Default value</span></span> | <span data-ttu-id="047de-790">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="047de-791">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="047de-792">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-793">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-794">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="047de-795">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-795">Empty</span></span> | <span data-ttu-id="047de-796">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="047de-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="047de-797">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-797">Empty</span></span> | <span data-ttu-id="047de-798">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="047de-799">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-799">Empty</span></span> | <span data-ttu-id="047de-800">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="047de-801">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-801">5 seconds</span></span> | <span data-ttu-id="047de-802">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-802">WebSockets only.</span></span> <span data-ttu-id="047de-803">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="047de-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="047de-804">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="047de-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="047de-805">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-805">Empty</span></span> | <span data-ttu-id="047de-806">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="047de-807">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="047de-808">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="047de-809">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="047de-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="047de-810">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="047de-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="047de-811">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="047de-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="047de-812">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="047de-813">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="047de-814">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="047de-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="047de-815">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="047de-816">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="047de-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="047de-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-818">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-818">JavaScript Option</span></span> | <span data-ttu-id="047de-819">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-819">Default Value</span></span> | <span data-ttu-id="047de-820">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="047de-821">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="047de-822">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="047de-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="047de-823">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-824">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-825">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-826">Java</span><span class="sxs-lookup"><span data-stu-id="047de-826">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-827">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="047de-827">Java Option</span></span> | <span data-ttu-id="047de-828">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-828">Default Value</span></span> | <span data-ttu-id="047de-829">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="047de-830">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="047de-831">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-832">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-833">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="047de-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="047de-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="047de-835">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-835">Empty</span></span> | <span data-ttu-id="047de-836">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="047de-837">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="047de-838">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="047de-839">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="047de-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="047de-840">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="047de-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="047de-841">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="047de-842">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="047de-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="047de-843">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="047de-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="047de-844">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="047de-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="047de-845">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="047de-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="047de-846">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="047de-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="047de-847">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="047de-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="047de-848">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="047de-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="047de-849">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="047de-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="047de-850">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="047de-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="047de-851">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="047de-852">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-852">MessagePack serialization options</span></span>

<span data-ttu-id="047de-853">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="047de-854">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-855">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="047de-856">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="047de-856">Configure server options</span></span>

<span data-ttu-id="047de-857">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="047de-858">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-858">Option</span></span> | <span data-ttu-id="047de-859">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-859">Default Value</span></span> | <span data-ttu-id="047de-860">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="047de-861">30 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-861">30 seconds</span></span> | <span data-ttu-id="047de-862">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="047de-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="047de-863">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="047de-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="047de-864">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="047de-865">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-865">15 seconds</span></span> | <span data-ttu-id="047de-866">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="047de-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="047de-867">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-868">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-869">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-869">15 seconds</span></span> | <span data-ttu-id="047de-870">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="047de-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="047de-871">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="047de-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="047de-872">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="047de-873">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="047de-873">All installed protocols</span></span> | <span data-ttu-id="047de-874">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="047de-874">Protocols supported by this hub.</span></span> <span data-ttu-id="047de-875">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="047de-876">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="047de-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="047de-877">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="047de-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="047de-878">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="047de-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="047de-879">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="047de-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="047de-880">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="047de-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="047de-881">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="047de-882">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="047de-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="047de-883">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="047de-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="047de-884">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-884">Option</span></span> | <span data-ttu-id="047de-885">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-885">Default Value</span></span> | <span data-ttu-id="047de-886">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="047de-887">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-887">32 KB</span></span> | <span data-ttu-id="047de-888">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="047de-889">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="047de-890">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="047de-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="047de-891">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="047de-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="047de-892">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-892">32 KB</span></span> | <span data-ttu-id="047de-893">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="047de-894">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="047de-895">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-895">All Transports are enabled.</span></span> | <span data-ttu-id="047de-896">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="047de-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="047de-897">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-897">See below.</span></span> | <span data-ttu-id="047de-898">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="047de-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="047de-899">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-899">See below.</span></span> | <span data-ttu-id="047de-900">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="047de-901">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="047de-902">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-902">Option</span></span> | <span data-ttu-id="047de-903">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-903">Default Value</span></span> | <span data-ttu-id="047de-904">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="047de-905">90 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-905">90 seconds</span></span> | <span data-ttu-id="047de-906">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="047de-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="047de-907">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="047de-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="047de-908">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="047de-909">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-909">Option</span></span> | <span data-ttu-id="047de-910">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-910">Default Value</span></span> | <span data-ttu-id="047de-911">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="047de-912">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-912">5 seconds</span></span> | <span data-ttu-id="047de-913">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="047de-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="047de-914">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="047de-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="047de-915">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="047de-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="047de-916">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="047de-916">Configure client options</span></span>

<span data-ttu-id="047de-917">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="047de-918">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="047de-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="047de-919">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="047de-919">Configure logging</span></span>

<span data-ttu-id="047de-920">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="047de-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="047de-921">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="047de-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="047de-922">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="047de-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-923">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="047de-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="047de-924">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="047de-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="047de-925">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="047de-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="047de-926">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="047de-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="047de-927">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="047de-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="047de-928">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="047de-929">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="047de-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="047de-930">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="047de-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="047de-931">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="047de-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="047de-932">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="047de-933">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="047de-934">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="047de-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="047de-935">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="047de-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="047de-936">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="047de-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="047de-937">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="047de-937">Configure allowed transports</span></span>

<span data-ttu-id="047de-938">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="047de-939">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="047de-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="047de-940">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-940">All transports are enabled by default.</span></span>

<span data-ttu-id="047de-941">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="047de-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="047de-942">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="047de-943">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="047de-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="047de-944">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="047de-944">Configure bearer authentication</span></span>

<span data-ttu-id="047de-945">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="047de-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="047de-946">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="047de-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="047de-947">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="047de-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="047de-948">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="047de-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="047de-949">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="047de-950">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="047de-951">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="047de-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="047de-952">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="047de-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="047de-953">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="047de-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="047de-954">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="047de-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="047de-955">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="047de-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-956">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-957">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-957">Option</span></span> | <span data-ttu-id="047de-958">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-958">Default value</span></span> | <span data-ttu-id="047de-959">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="047de-960">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-961">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-961">Timeout for server activity.</span></span> <span data-ttu-id="047de-962">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-963">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-964">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="047de-965">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-965">15 seconds</span></span> | <span data-ttu-id="047de-966">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-967">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-968">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-969">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-970">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-970">15 seconds</span></span> | <span data-ttu-id="047de-971">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-972">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-973">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="047de-974">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="047de-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="047de-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-976">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-976">Option</span></span> | <span data-ttu-id="047de-977">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-977">Default value</span></span> | <span data-ttu-id="047de-978">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="047de-979">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-980">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-980">Timeout for server activity.</span></span> <span data-ttu-id="047de-981">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="047de-982">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-983">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="047de-984">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="047de-985">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-986">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-987">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-988">Java</span><span class="sxs-lookup"><span data-stu-id="047de-988">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-989">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-989">Option</span></span> | <span data-ttu-id="047de-990">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-990">Default value</span></span> | <span data-ttu-id="047de-991">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="047de-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="047de-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="047de-993">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-994">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-994">Timeout for server activity.</span></span> <span data-ttu-id="047de-995">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-996">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-997">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="047de-998">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-998">15 seconds</span></span> | <span data-ttu-id="047de-999">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-1000">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-1001">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-1002">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="047de-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="047de-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="047de-1004">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="047de-1005">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="047de-1006">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="047de-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="047de-1007">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="047de-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="047de-1008">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="047de-1008">Configure additional options</span></span>

<span data-ttu-id="047de-1009">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="047de-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-1011">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="047de-1011">.NET Option</span></span> |  <span data-ttu-id="047de-1012">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1012">Default value</span></span> | <span data-ttu-id="047de-1013">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="047de-1014">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="047de-1015">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-1016">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-1017">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="047de-1018">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1018">Empty</span></span> | <span data-ttu-id="047de-1019">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="047de-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="047de-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1020">Empty</span></span> | <span data-ttu-id="047de-1021">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="047de-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1022">Empty</span></span> | <span data-ttu-id="047de-1023">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="047de-1024">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-1024">5 seconds</span></span> | <span data-ttu-id="047de-1025">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-1025">WebSockets only.</span></span> <span data-ttu-id="047de-1026">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="047de-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="047de-1027">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="047de-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="047de-1028">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1028">Empty</span></span> | <span data-ttu-id="047de-1029">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="047de-1030">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="047de-1031">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="047de-1032">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="047de-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="047de-1033">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="047de-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="047de-1034">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="047de-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="047de-1035">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="047de-1036">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="047de-1037">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="047de-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="047de-1038">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="047de-1039">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="047de-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="047de-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-1041">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-1041">JavaScript Option</span></span> | <span data-ttu-id="047de-1042">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1042">Default Value</span></span> | <span data-ttu-id="047de-1043">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="047de-1044">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="047de-1045">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="047de-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="047de-1046">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-1047">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-1048">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-1049">Java</span><span class="sxs-lookup"><span data-stu-id="047de-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-1050">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="047de-1050">Java Option</span></span> | <span data-ttu-id="047de-1051">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1051">Default Value</span></span> | <span data-ttu-id="047de-1052">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="047de-1053">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="047de-1054">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-1055">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-1056">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="047de-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="047de-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="047de-1058">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1058">Empty</span></span> | <span data-ttu-id="047de-1059">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="047de-1060">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="047de-1061">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="047de-1062">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="047de-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="047de-1063">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="047de-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="047de-1064">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="047de-1065">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="047de-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="047de-1066">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="047de-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="047de-1067">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="047de-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="047de-1068">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="047de-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="047de-1069">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="047de-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="047de-1070">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="047de-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="047de-1071">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="047de-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="047de-1072">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="047de-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="047de-1073">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="047de-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="047de-1074">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="047de-1075">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="047de-1075">MessagePack serialization options</span></span>

<span data-ttu-id="047de-1076">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="047de-1077">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="047de-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-1078">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="047de-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="047de-1079">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="047de-1079">Configure server options</span></span>

<span data-ttu-id="047de-1080">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="047de-1081">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-1081">Option</span></span> | <span data-ttu-id="047de-1082">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1082">Default Value</span></span> | <span data-ttu-id="047de-1083">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="047de-1084">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-1084">15 seconds</span></span> | <span data-ttu-id="047de-1085">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="047de-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="047de-1086">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-1087">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="047de-1088">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-1088">15 seconds</span></span> | <span data-ttu-id="047de-1089">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="047de-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="047de-1090">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="047de-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="047de-1091">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="047de-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="047de-1092">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="047de-1092">All installed protocols</span></span> | <span data-ttu-id="047de-1093">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="047de-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="047de-1094">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="047de-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="047de-1095">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="047de-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="047de-1096">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="047de-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="047de-1097">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="047de-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="047de-1098">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="047de-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="047de-1099">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="047de-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="047de-1100">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="047de-1101">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="047de-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="047de-1102">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="047de-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="047de-1103">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-1103">Option</span></span> | <span data-ttu-id="047de-1104">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1104">Default Value</span></span> | <span data-ttu-id="047de-1105">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="047de-1106">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-1106">32 KB</span></span> | <span data-ttu-id="047de-1107">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="047de-1108">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="047de-1109">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="047de-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="047de-1110">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="047de-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="047de-1111">32 КБ</span><span class="sxs-lookup"><span data-stu-id="047de-1111">32 KB</span></span> | <span data-ttu-id="047de-1112">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="047de-1113">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="047de-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="047de-1114">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-1114">All Transports are enabled.</span></span> | <span data-ttu-id="047de-1115">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="047de-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="047de-1116">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-1116">See below.</span></span> | <span data-ttu-id="047de-1117">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="047de-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="047de-1118">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="047de-1118">See below.</span></span> | <span data-ttu-id="047de-1119">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="047de-1120">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="047de-1121">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-1121">Option</span></span> | <span data-ttu-id="047de-1122">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1122">Default Value</span></span> | <span data-ttu-id="047de-1123">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="047de-1124">90 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-1124">90 seconds</span></span> | <span data-ttu-id="047de-1125">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="047de-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="047de-1126">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="047de-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="047de-1127">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="047de-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="047de-1128">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-1128">Option</span></span> | <span data-ttu-id="047de-1129">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1129">Default Value</span></span> | <span data-ttu-id="047de-1130">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="047de-1131">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-1131">5 seconds</span></span> | <span data-ttu-id="047de-1132">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="047de-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="047de-1133">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="047de-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="047de-1134">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="047de-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="047de-1135">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="047de-1135">Configure client options</span></span>

<span data-ttu-id="047de-1136">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="047de-1137">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="047de-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="047de-1138">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="047de-1138">Configure logging</span></span>

<span data-ttu-id="047de-1139">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="047de-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="047de-1140">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="047de-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="047de-1141">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="047de-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="047de-1142">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="047de-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="047de-1143">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="047de-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="047de-1144">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="047de-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="047de-1145">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="047de-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="047de-1146">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="047de-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="047de-1147">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="047de-1148">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="047de-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="047de-1149">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="047de-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="047de-1150">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="047de-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="047de-1151">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="047de-1152">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="047de-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="047de-1153">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="047de-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="047de-1154">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="047de-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="047de-1155">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="047de-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="047de-1156">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="047de-1156">Configure allowed transports</span></span>

<span data-ttu-id="047de-1157">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="047de-1158">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="047de-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="047de-1159">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="047de-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="047de-1160">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="047de-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="047de-1161">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="047de-1162">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="047de-1162">Configure bearer authentication</span></span>

<span data-ttu-id="047de-1163">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="047de-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="047de-1164">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="047de-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="047de-1165">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="047de-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="047de-1166">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="047de-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="047de-1167">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="047de-1168">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="047de-1169">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="047de-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="047de-1170">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="047de-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="047de-1171">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="047de-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="047de-1172">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="047de-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="047de-1173">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="047de-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-1175">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-1175">Option</span></span> | <span data-ttu-id="047de-1176">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1176">Default value</span></span> | <span data-ttu-id="047de-1177">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="047de-1178">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-1179">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-1179">Timeout for server activity.</span></span> <span data-ttu-id="047de-1180">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-1181">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-1182">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="047de-1183">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-1183">15 seconds</span></span> | <span data-ttu-id="047de-1184">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-1185">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="047de-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="047de-1186">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-1187">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="047de-1188">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="047de-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="047de-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-1190">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-1190">Option</span></span> | <span data-ttu-id="047de-1191">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1191">Default value</span></span> | <span data-ttu-id="047de-1192">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="047de-1193">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-1194">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-1194">Timeout for server activity.</span></span> <span data-ttu-id="047de-1195">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="047de-1196">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-1197">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-1198">Java</span><span class="sxs-lookup"><span data-stu-id="047de-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-1199">Параметр</span><span class="sxs-lookup"><span data-stu-id="047de-1199">Option</span></span> | <span data-ttu-id="047de-1200">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1200">Default value</span></span> | <span data-ttu-id="047de-1201">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="047de-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="047de-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="047de-1203">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="047de-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="047de-1204">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-1204">Timeout for server activity.</span></span> <span data-ttu-id="047de-1205">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-1206">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="047de-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="047de-1207">Рекомендуемое значение — это число, по крайней мере, двойное `KeepAliveInterval` значение сервера, которое позволяет получить время для проверки связи.</span><span class="sxs-lookup"><span data-stu-id="047de-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="047de-1208">15 секунд</span><span class="sxs-lookup"><span data-stu-id="047de-1208">15 seconds</span></span> | <span data-ttu-id="047de-1209">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="047de-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="047de-1210">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="047de-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="047de-1211">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="047de-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="047de-1212">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="047de-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="047de-1213">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="047de-1213">Configure additional options</span></span>

<span data-ttu-id="047de-1214">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="047de-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="047de-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="047de-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="047de-1216">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="047de-1216">.NET Option</span></span> |  <span data-ttu-id="047de-1217">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1217">Default value</span></span> | <span data-ttu-id="047de-1218">Описание</span><span class="sxs-lookup"><span data-stu-id="047de-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="047de-1219">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="047de-1220">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-1221">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-1222">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="047de-1223">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1223">Empty</span></span> | <span data-ttu-id="047de-1224">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="047de-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="047de-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1225">Empty</span></span> | <span data-ttu-id="047de-1226">Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="047de-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1227">Empty</span></span> | <span data-ttu-id="047de-1228">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="047de-1229">5 с</span><span class="sxs-lookup"><span data-stu-id="047de-1229">5 seconds</span></span> | <span data-ttu-id="047de-1230">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-1230">WebSockets only.</span></span> <span data-ttu-id="047de-1231">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="047de-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="047de-1232">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="047de-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="047de-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1233">Empty</span></span> | <span data-ttu-id="047de-1234">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="047de-1235">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="047de-1236">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="047de-1237">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="047de-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="047de-1238">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="047de-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="047de-1239">**При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="047de-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="047de-1240">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="047de-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="047de-1241">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="047de-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="047de-1242">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="047de-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="047de-1243">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="047de-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="047de-1244">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="047de-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="047de-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="047de-1246">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="047de-1246">JavaScript Option</span></span> | <span data-ttu-id="047de-1247">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1247">Default Value</span></span> | <span data-ttu-id="047de-1248">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="047de-1249">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="047de-1250">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="047de-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="047de-1251">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-1252">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-1253">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="047de-1254">Java</span><span class="sxs-lookup"><span data-stu-id="047de-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="047de-1255">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="047de-1255">Java Option</span></span> | <span data-ttu-id="047de-1256">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="047de-1256">Default Value</span></span> | <span data-ttu-id="047de-1257">Описание:</span><span class="sxs-lookup"><span data-stu-id="047de-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="047de-1258">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="047de-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="047de-1259">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="047de-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="047de-1260">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="047de-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="047de-1261">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="047de-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="047de-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="047de-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="047de-1263">Empty</span><span class="sxs-lookup"><span data-stu-id="047de-1263">Empty</span></span> | <span data-ttu-id="047de-1264">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="047de-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="047de-1265">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="047de-1266">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="047de-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="047de-1267">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="047de-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="047de-1268">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="047de-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
