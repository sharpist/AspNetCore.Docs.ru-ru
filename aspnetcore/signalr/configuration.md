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
ms.openlocfilehash: fc0e6398884bb5c3b806a587a8a361d7f279461f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625561"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="c8ad9-103">Конфигурация SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8ad9-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c8ad9-104">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-105">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c8ad9-106">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c8ad9-107">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c8ad9-108">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c8ad9-109">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c8ad9-110">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c8ad9-111">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c8ad9-112">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c8ad9-113">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c8ad9-114">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c8ad9-115">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c8ad9-116">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c8ad9-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c8ad9-117">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c8ad9-118">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-118">MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-119">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c8ad9-120">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-121">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c8ad9-122">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="c8ad9-122">Configure server options</span></span>

<span data-ttu-id="c8ad9-123">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c8ad9-124">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-124">Option</span></span> | <span data-ttu-id="c8ad9-125">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-125">Default Value</span></span> | <span data-ttu-id="c8ad9-126">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c8ad9-127">30 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-127">30 seconds</span></span> | <span data-ttu-id="c8ad9-128">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="c8ad9-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c8ad9-129">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c8ad9-130">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-131">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-131">15 seconds</span></span> | <span data-ttu-id="c8ad9-132">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c8ad9-133">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-134">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-135">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-135">15 seconds</span></span> | <span data-ttu-id="c8ad9-136">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c8ad9-137">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c8ad9-138">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c8ad9-139">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-139">All installed protocols</span></span> | <span data-ttu-id="c8ad9-140">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-140">Protocols supported by this hub.</span></span> <span data-ttu-id="c8ad9-141">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c8ad9-142">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c8ad9-143">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c8ad9-144">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c8ad9-145">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c8ad9-146">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-146">32 KB</span></span> | <span data-ttu-id="c8ad9-147">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c8ad9-148">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c8ad9-149">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c8ad9-150">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="c8ad9-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c8ad9-151">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c8ad9-152">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c8ad9-153">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c8ad9-154">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-154">Option</span></span> | <span data-ttu-id="c8ad9-155">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-155">Default Value</span></span> | <span data-ttu-id="c8ad9-156">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c8ad9-157">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-157">32 KB</span></span> | <span data-ttu-id="c8ad9-158">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c8ad9-159">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c8ad9-160">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c8ad9-161">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c8ad9-162">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-162">32 KB</span></span> | <span data-ttu-id="c8ad9-163">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c8ad9-164">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c8ad9-165">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-165">All Transports are enabled.</span></span> | <span data-ttu-id="c8ad9-166">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c8ad9-167">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-167">See below.</span></span> | <span data-ttu-id="c8ad9-168">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c8ad9-169">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-169">See below.</span></span> | <span data-ttu-id="c8ad9-170">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c8ad9-171">0</span><span class="sxs-lookup"><span data-stu-id="c8ad9-171">0</span></span> | <span data-ttu-id="c8ad9-172">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c8ad9-173">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c8ad9-174">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c8ad9-175">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-175">Option</span></span> | <span data-ttu-id="c8ad9-176">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-176">Default Value</span></span> | <span data-ttu-id="c8ad9-177">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c8ad9-178">90 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-178">90 seconds</span></span> | <span data-ttu-id="c8ad9-179">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c8ad9-180">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c8ad9-181">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c8ad9-182">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-182">Option</span></span> | <span data-ttu-id="c8ad9-183">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-183">Default Value</span></span> | <span data-ttu-id="c8ad9-184">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c8ad9-185">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-185">5 seconds</span></span> | <span data-ttu-id="c8ad9-186">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c8ad9-187">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c8ad9-188">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c8ad9-189">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="c8ad9-189">Configure client options</span></span>

<span data-ttu-id="c8ad9-190">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c8ad9-191">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c8ad9-192">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="c8ad9-192">Configure logging</span></span>

<span data-ttu-id="c8ad9-193">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c8ad9-194">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c8ad9-195">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-196">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c8ad9-197">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c8ad9-198">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c8ad9-199">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c8ad9-200">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c8ad9-201">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c8ad9-202">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c8ad9-203">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c8ad9-204">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c8ad9-205">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-205">The following table lists the available log levels.</span></span> <span data-ttu-id="c8ad9-206">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c8ad9-207">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c8ad9-208">Строка</span><span class="sxs-lookup"><span data-stu-id="c8ad9-208">String</span></span>                      | <span data-ttu-id="c8ad9-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c8ad9-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c8ad9-210">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c8ad9-211">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c8ad9-212">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c8ad9-213">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c8ad9-214">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c8ad9-215">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c8ad9-216">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c8ad9-217">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c8ad9-218">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c8ad9-219">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="c8ad9-219">Configure allowed transports</span></span>

<span data-ttu-id="c8ad9-220">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c8ad9-221">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c8ad9-222">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-222">All transports are enabled by default.</span></span>

<span data-ttu-id="c8ad9-223">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c8ad9-224">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c8ad9-225">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c8ad9-226">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c8ad9-227">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c8ad9-228">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c8ad9-229">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="c8ad9-229">Configure bearer authentication</span></span>

<span data-ttu-id="c8ad9-230">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c8ad9-231">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c8ad9-232">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c8ad9-233">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c8ad9-234">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c8ad9-235">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c8ad9-236">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c8ad9-237">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c8ad9-238">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c8ad9-239">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="c8ad9-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c8ad9-240">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-241">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-242">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-242">Option</span></span> | <span data-ttu-id="c8ad9-243">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-243">Default value</span></span> | <span data-ttu-id="c8ad9-244">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c8ad9-245">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-246">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-246">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-247">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-248">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-249">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-250">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-250">15 seconds</span></span> | <span data-ttu-id="c8ad9-251">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-252">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-253">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-254">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-255">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-255">15 seconds</span></span> | <span data-ttu-id="c8ad9-256">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-257">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-258">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c8ad9-259">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-261">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-261">Option</span></span> | <span data-ttu-id="c8ad9-262">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-262">Default value</span></span> | <span data-ttu-id="c8ad9-263">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c8ad9-264">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-265">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-265">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-266">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c8ad9-267">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-268">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c8ad9-269">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-270">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-271">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-272">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-273">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-273">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-274">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-274">Option</span></span> | <span data-ttu-id="c8ad9-275">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-275">Default value</span></span> | <span data-ttu-id="c8ad9-276">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c8ad9-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c8ad9-278">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-279">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-279">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-280">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-281">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-282">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c8ad9-283">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-283">15 seconds</span></span> | <span data-ttu-id="c8ad9-284">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-285">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-286">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-287">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c8ad9-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c8ad9-289">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-290">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-291">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-292">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c8ad9-293">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="c8ad9-293">Configure additional options</span></span>

<span data-ttu-id="c8ad9-294">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-295">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-296">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-296">.NET Option</span></span> |  <span data-ttu-id="c8ad9-297">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-297">Default value</span></span> | <span data-ttu-id="c8ad9-298">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-299">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c8ad9-300">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-301">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-302">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c8ad9-303">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-303">Empty</span></span> | <span data-ttu-id="c8ad9-304">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c8ad9-305">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-305">Empty</span></span> | <span data-ttu-id="c8ad9-306">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c8ad9-307">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-307">Empty</span></span> | <span data-ttu-id="c8ad9-308">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c8ad9-309">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-309">5 seconds</span></span> | <span data-ttu-id="c8ad9-310">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-310">WebSockets only.</span></span> <span data-ttu-id="c8ad9-311">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c8ad9-312">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c8ad9-313">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-313">Empty</span></span> | <span data-ttu-id="c8ad9-314">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c8ad9-315">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c8ad9-316">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="c8ad9-317">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c8ad9-318">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c8ad9-319">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="c8ad9-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c8ad9-320">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c8ad9-321">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c8ad9-322">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c8ad9-323">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c8ad9-324">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-326">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-326">JavaScript Option</span></span> | <span data-ttu-id="c8ad9-327">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-327">Default Value</span></span> | <span data-ttu-id="c8ad9-328">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c8ad9-329">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="c8ad9-330">Словарь заголовков, отправленных с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="c8ad9-331">Отправка заголовков в браузере не работает для WebSockets или <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> потока.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c8ad9-332">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c8ad9-333">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-334">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-335">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="c8ad9-336">Указывает, будут ли учетные данные отправляться с запросом CORS.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="c8ad9-337">Служба приложений Azure использует cookie для прикрепленных сеансов s и требует, чтобы этот параметр был включен правильно.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="c8ad9-338">Дополнительные сведения о CORS с SignalR см. в разделе <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-339">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-339">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-340">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-340">Java Option</span></span> | <span data-ttu-id="c8ad9-341">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-341">Default Value</span></span> | <span data-ttu-id="c8ad9-342">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-343">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c8ad9-344">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-345">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-346">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c8ad9-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c8ad9-348">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-348">Empty</span></span> | <span data-ttu-id="c8ad9-349">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c8ad9-350">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c8ad9-351">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c8ad9-352">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c8ad9-353">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c8ad9-354">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-355">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c8ad9-356">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c8ad9-357">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c8ad9-358">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c8ad9-359">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c8ad9-360">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c8ad9-361">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c8ad9-362">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c8ad9-363">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c8ad9-364">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c8ad9-365">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c8ad9-366">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c8ad9-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c8ad9-367">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c8ad9-368">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-368">MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-369">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c8ad9-370">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-371">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c8ad9-372">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="c8ad9-372">Configure server options</span></span>

<span data-ttu-id="c8ad9-373">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c8ad9-374">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-374">Option</span></span> | <span data-ttu-id="c8ad9-375">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-375">Default Value</span></span> | <span data-ttu-id="c8ad9-376">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c8ad9-377">30 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-377">30 seconds</span></span> | <span data-ttu-id="c8ad9-378">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="c8ad9-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c8ad9-379">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c8ad9-380">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-381">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-381">15 seconds</span></span> | <span data-ttu-id="c8ad9-382">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c8ad9-383">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-384">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-385">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-385">15 seconds</span></span> | <span data-ttu-id="c8ad9-386">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c8ad9-387">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c8ad9-388">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c8ad9-389">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-389">All installed protocols</span></span> | <span data-ttu-id="c8ad9-390">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-390">Protocols supported by this hub.</span></span> <span data-ttu-id="c8ad9-391">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c8ad9-392">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c8ad9-393">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c8ad9-394">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c8ad9-395">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c8ad9-396">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-396">32 KB</span></span> | <span data-ttu-id="c8ad9-397">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c8ad9-398">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c8ad9-399">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c8ad9-400">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="c8ad9-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c8ad9-401">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c8ad9-402">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c8ad9-403">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c8ad9-404">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-404">Option</span></span> | <span data-ttu-id="c8ad9-405">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-405">Default Value</span></span> | <span data-ttu-id="c8ad9-406">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c8ad9-407">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-407">32 KB</span></span> | <span data-ttu-id="c8ad9-408">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c8ad9-409">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c8ad9-410">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c8ad9-411">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c8ad9-412">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-412">32 KB</span></span> | <span data-ttu-id="c8ad9-413">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c8ad9-414">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c8ad9-415">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-415">All Transports are enabled.</span></span> | <span data-ttu-id="c8ad9-416">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c8ad9-417">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-417">See below.</span></span> | <span data-ttu-id="c8ad9-418">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c8ad9-419">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-419">See below.</span></span> | <span data-ttu-id="c8ad9-420">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c8ad9-421">0</span><span class="sxs-lookup"><span data-stu-id="c8ad9-421">0</span></span> | <span data-ttu-id="c8ad9-422">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c8ad9-423">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c8ad9-424">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c8ad9-425">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-425">Option</span></span> | <span data-ttu-id="c8ad9-426">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-426">Default Value</span></span> | <span data-ttu-id="c8ad9-427">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c8ad9-428">90 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-428">90 seconds</span></span> | <span data-ttu-id="c8ad9-429">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c8ad9-430">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c8ad9-431">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c8ad9-432">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-432">Option</span></span> | <span data-ttu-id="c8ad9-433">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-433">Default Value</span></span> | <span data-ttu-id="c8ad9-434">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c8ad9-435">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-435">5 seconds</span></span> | <span data-ttu-id="c8ad9-436">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c8ad9-437">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c8ad9-438">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c8ad9-439">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="c8ad9-439">Configure client options</span></span>

<span data-ttu-id="c8ad9-440">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c8ad9-441">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c8ad9-442">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="c8ad9-442">Configure logging</span></span>

<span data-ttu-id="c8ad9-443">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c8ad9-444">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c8ad9-445">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-446">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c8ad9-447">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c8ad9-448">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c8ad9-449">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c8ad9-450">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c8ad9-451">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c8ad9-452">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c8ad9-453">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c8ad9-454">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c8ad9-455">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-455">The following table lists the available log levels.</span></span> <span data-ttu-id="c8ad9-456">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c8ad9-457">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c8ad9-458">Строка</span><span class="sxs-lookup"><span data-stu-id="c8ad9-458">String</span></span>                      | <span data-ttu-id="c8ad9-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c8ad9-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c8ad9-460">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c8ad9-461">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c8ad9-462">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c8ad9-463">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c8ad9-464">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c8ad9-465">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c8ad9-466">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c8ad9-467">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c8ad9-468">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c8ad9-469">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="c8ad9-469">Configure allowed transports</span></span>

<span data-ttu-id="c8ad9-470">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c8ad9-471">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c8ad9-472">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-472">All transports are enabled by default.</span></span>

<span data-ttu-id="c8ad9-473">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c8ad9-474">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c8ad9-475">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c8ad9-476">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c8ad9-477">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c8ad9-478">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c8ad9-479">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="c8ad9-479">Configure bearer authentication</span></span>

<span data-ttu-id="c8ad9-480">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c8ad9-481">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c8ad9-482">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c8ad9-483">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c8ad9-484">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c8ad9-485">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c8ad9-486">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c8ad9-487">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c8ad9-488">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c8ad9-489">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="c8ad9-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c8ad9-490">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-491">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-492">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-492">Option</span></span> | <span data-ttu-id="c8ad9-493">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-493">Default value</span></span> | <span data-ttu-id="c8ad9-494">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c8ad9-495">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-496">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-496">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-497">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-498">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-499">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-500">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-500">15 seconds</span></span> | <span data-ttu-id="c8ad9-501">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-502">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-503">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-504">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-505">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-505">15 seconds</span></span> | <span data-ttu-id="c8ad9-506">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-507">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-508">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c8ad9-509">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-511">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-511">Option</span></span> | <span data-ttu-id="c8ad9-512">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-512">Default value</span></span> | <span data-ttu-id="c8ad9-513">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c8ad9-514">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-515">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-515">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-516">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c8ad9-517">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-518">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c8ad9-519">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-520">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-521">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-522">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-523">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-523">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-524">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-524">Option</span></span> | <span data-ttu-id="c8ad9-525">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-525">Default value</span></span> | <span data-ttu-id="c8ad9-526">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c8ad9-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c8ad9-528">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-529">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-529">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-530">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-531">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-532">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c8ad9-533">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-533">15 seconds</span></span> | <span data-ttu-id="c8ad9-534">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-535">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-536">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-537">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c8ad9-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c8ad9-539">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-540">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-541">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-542">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c8ad9-543">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="c8ad9-543">Configure additional options</span></span>

<span data-ttu-id="c8ad9-544">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-545">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-546">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-546">.NET Option</span></span> |  <span data-ttu-id="c8ad9-547">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-547">Default value</span></span> | <span data-ttu-id="c8ad9-548">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-549">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c8ad9-550">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-551">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-552">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c8ad9-553">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-553">Empty</span></span> | <span data-ttu-id="c8ad9-554">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c8ad9-555">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-555">Empty</span></span> | <span data-ttu-id="c8ad9-556">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c8ad9-557">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-557">Empty</span></span> | <span data-ttu-id="c8ad9-558">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c8ad9-559">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-559">5 seconds</span></span> | <span data-ttu-id="c8ad9-560">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-560">WebSockets only.</span></span> <span data-ttu-id="c8ad9-561">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c8ad9-562">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c8ad9-563">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-563">Empty</span></span> | <span data-ttu-id="c8ad9-564">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c8ad9-565">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c8ad9-566">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="c8ad9-567">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c8ad9-568">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c8ad9-569">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="c8ad9-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c8ad9-570">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c8ad9-571">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c8ad9-572">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c8ad9-573">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c8ad9-574">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-576">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-576">JavaScript Option</span></span> | <span data-ttu-id="c8ad9-577">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-577">Default Value</span></span> | <span data-ttu-id="c8ad9-578">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c8ad9-579">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c8ad9-580">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c8ad9-581">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-582">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-583">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-584">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-584">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-585">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-585">Java Option</span></span> | <span data-ttu-id="c8ad9-586">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-586">Default Value</span></span> | <span data-ttu-id="c8ad9-587">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-588">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c8ad9-589">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-590">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-591">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c8ad9-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c8ad9-593">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-593">Empty</span></span> | <span data-ttu-id="c8ad9-594">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c8ad9-595">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c8ad9-596">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c8ad9-597">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c8ad9-598">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c8ad9-599">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-600">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c8ad9-601">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c8ad9-602">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c8ad9-603">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c8ad9-604">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c8ad9-605">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c8ad9-606">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c8ad9-607">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="c8ad9-608">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c8ad9-609">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c8ad9-610">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c8ad9-611">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c8ad9-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c8ad9-612">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c8ad9-613">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-613">MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-614">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c8ad9-615">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-616">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c8ad9-617">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="c8ad9-617">Configure server options</span></span>

<span data-ttu-id="c8ad9-618">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c8ad9-619">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-619">Option</span></span> | <span data-ttu-id="c8ad9-620">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-620">Default Value</span></span> | <span data-ttu-id="c8ad9-621">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c8ad9-622">30 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-622">30 seconds</span></span> | <span data-ttu-id="c8ad9-623">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="c8ad9-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c8ad9-624">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c8ad9-625">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-626">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-626">15 seconds</span></span> | <span data-ttu-id="c8ad9-627">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c8ad9-628">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-629">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-630">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-630">15 seconds</span></span> | <span data-ttu-id="c8ad9-631">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c8ad9-632">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c8ad9-633">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c8ad9-634">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-634">All installed protocols</span></span> | <span data-ttu-id="c8ad9-635">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-635">Protocols supported by this hub.</span></span> <span data-ttu-id="c8ad9-636">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c8ad9-637">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c8ad9-638">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c8ad9-639">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c8ad9-640">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c8ad9-641">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-641">32 KB</span></span> | <span data-ttu-id="c8ad9-642">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c8ad9-643">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c8ad9-644">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c8ad9-645">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="c8ad9-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c8ad9-646">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c8ad9-647">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c8ad9-648">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c8ad9-649">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-649">Option</span></span> | <span data-ttu-id="c8ad9-650">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-650">Default Value</span></span> | <span data-ttu-id="c8ad9-651">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c8ad9-652">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-652">32 KB</span></span> | <span data-ttu-id="c8ad9-653">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c8ad9-654">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c8ad9-655">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c8ad9-656">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c8ad9-657">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-657">32 KB</span></span> | <span data-ttu-id="c8ad9-658">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c8ad9-659">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c8ad9-660">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-660">All Transports are enabled.</span></span> | <span data-ttu-id="c8ad9-661">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c8ad9-662">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-662">See below.</span></span> | <span data-ttu-id="c8ad9-663">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c8ad9-664">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-664">See below.</span></span> | <span data-ttu-id="c8ad9-665">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c8ad9-666">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c8ad9-667">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-667">Option</span></span> | <span data-ttu-id="c8ad9-668">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-668">Default Value</span></span> | <span data-ttu-id="c8ad9-669">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c8ad9-670">90 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-670">90 seconds</span></span> | <span data-ttu-id="c8ad9-671">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c8ad9-672">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c8ad9-673">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c8ad9-674">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-674">Option</span></span> | <span data-ttu-id="c8ad9-675">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-675">Default Value</span></span> | <span data-ttu-id="c8ad9-676">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c8ad9-677">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-677">5 seconds</span></span> | <span data-ttu-id="c8ad9-678">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c8ad9-679">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c8ad9-680">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c8ad9-681">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="c8ad9-681">Configure client options</span></span>

<span data-ttu-id="c8ad9-682">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c8ad9-683">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c8ad9-684">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="c8ad9-684">Configure logging</span></span>

<span data-ttu-id="c8ad9-685">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c8ad9-686">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c8ad9-687">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-688">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c8ad9-689">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c8ad9-690">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c8ad9-691">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c8ad9-692">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c8ad9-693">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c8ad9-694">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c8ad9-695">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c8ad9-696">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c8ad9-697">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-697">The following table lists the available log levels.</span></span> <span data-ttu-id="c8ad9-698">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c8ad9-699">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c8ad9-700">Строка</span><span class="sxs-lookup"><span data-stu-id="c8ad9-700">String</span></span>                      | <span data-ttu-id="c8ad9-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c8ad9-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c8ad9-702">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c8ad9-703">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c8ad9-704">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c8ad9-705">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c8ad9-706">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c8ad9-707">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c8ad9-708">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c8ad9-709">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c8ad9-710">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c8ad9-711">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="c8ad9-711">Configure allowed transports</span></span>

<span data-ttu-id="c8ad9-712">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c8ad9-713">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c8ad9-714">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-714">All transports are enabled by default.</span></span>

<span data-ttu-id="c8ad9-715">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c8ad9-716">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c8ad9-717">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c8ad9-718">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c8ad9-719">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c8ad9-720">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c8ad9-721">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="c8ad9-721">Configure bearer authentication</span></span>

<span data-ttu-id="c8ad9-722">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c8ad9-723">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c8ad9-724">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c8ad9-725">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c8ad9-726">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c8ad9-727">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c8ad9-728">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c8ad9-729">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c8ad9-730">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c8ad9-731">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="c8ad9-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c8ad9-732">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-733">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-734">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-734">Option</span></span> | <span data-ttu-id="c8ad9-735">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-735">Default value</span></span> | <span data-ttu-id="c8ad9-736">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c8ad9-737">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-738">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-738">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-739">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-740">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-741">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-742">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-742">15 seconds</span></span> | <span data-ttu-id="c8ad9-743">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-744">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-745">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-746">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-747">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-747">15 seconds</span></span> | <span data-ttu-id="c8ad9-748">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-749">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-750">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c8ad9-751">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-753">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-753">Option</span></span> | <span data-ttu-id="c8ad9-754">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-754">Default value</span></span> | <span data-ttu-id="c8ad9-755">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c8ad9-756">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-757">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-757">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-758">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c8ad9-759">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-760">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c8ad9-761">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-762">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-763">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-764">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-765">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-765">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-766">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-766">Option</span></span> | <span data-ttu-id="c8ad9-767">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-767">Default value</span></span> | <span data-ttu-id="c8ad9-768">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c8ad9-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c8ad9-770">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-771">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-771">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-772">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-773">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-774">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c8ad9-775">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-775">15 seconds</span></span> | <span data-ttu-id="c8ad9-776">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-777">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-778">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-779">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c8ad9-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c8ad9-781">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-782">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-783">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-784">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c8ad9-785">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="c8ad9-785">Configure additional options</span></span>

<span data-ttu-id="c8ad9-786">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-787">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-788">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-788">.NET Option</span></span> |  <span data-ttu-id="c8ad9-789">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-789">Default value</span></span> | <span data-ttu-id="c8ad9-790">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-791">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c8ad9-792">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-793">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-794">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c8ad9-795">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-795">Empty</span></span> | <span data-ttu-id="c8ad9-796">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c8ad9-797">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-797">Empty</span></span> | <span data-ttu-id="c8ad9-798">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c8ad9-799">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-799">Empty</span></span> | <span data-ttu-id="c8ad9-800">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c8ad9-801">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-801">5 seconds</span></span> | <span data-ttu-id="c8ad9-802">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-802">WebSockets only.</span></span> <span data-ttu-id="c8ad9-803">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c8ad9-804">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c8ad9-805">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-805">Empty</span></span> | <span data-ttu-id="c8ad9-806">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c8ad9-807">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c8ad9-808">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="c8ad9-809">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c8ad9-810">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c8ad9-811">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="c8ad9-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c8ad9-812">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c8ad9-813">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c8ad9-814">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c8ad9-815">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c8ad9-816">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-818">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-818">JavaScript Option</span></span> | <span data-ttu-id="c8ad9-819">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-819">Default Value</span></span> | <span data-ttu-id="c8ad9-820">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c8ad9-821">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c8ad9-822">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c8ad9-823">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-824">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-825">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-826">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-826">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-827">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-827">Java Option</span></span> | <span data-ttu-id="c8ad9-828">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-828">Default Value</span></span> | <span data-ttu-id="c8ad9-829">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-830">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c8ad9-831">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-832">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-833">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c8ad9-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c8ad9-835">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-835">Empty</span></span> | <span data-ttu-id="c8ad9-836">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c8ad9-837">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c8ad9-838">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c8ad9-839">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c8ad9-840">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c8ad9-841">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-842">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c8ad9-843">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c8ad9-844">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c8ad9-845">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c8ad9-846">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c8ad9-847">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c8ad9-848">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c8ad9-849">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c8ad9-850">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c8ad9-851">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c8ad9-852">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-852">MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-853">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c8ad9-854">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-855">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c8ad9-856">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="c8ad9-856">Configure server options</span></span>

<span data-ttu-id="c8ad9-857">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c8ad9-858">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-858">Option</span></span> | <span data-ttu-id="c8ad9-859">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-859">Default Value</span></span> | <span data-ttu-id="c8ad9-860">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c8ad9-861">30 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-861">30 seconds</span></span> | <span data-ttu-id="c8ad9-862">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="c8ad9-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c8ad9-863">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c8ad9-864">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-865">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-865">15 seconds</span></span> | <span data-ttu-id="c8ad9-866">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c8ad9-867">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-868">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-869">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-869">15 seconds</span></span> | <span data-ttu-id="c8ad9-870">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c8ad9-871">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c8ad9-872">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c8ad9-873">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-873">All installed protocols</span></span> | <span data-ttu-id="c8ad9-874">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-874">Protocols supported by this hub.</span></span> <span data-ttu-id="c8ad9-875">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c8ad9-876">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c8ad9-877">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c8ad9-878">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c8ad9-879">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c8ad9-880">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="c8ad9-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c8ad9-881">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c8ad9-882">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c8ad9-883">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c8ad9-884">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-884">Option</span></span> | <span data-ttu-id="c8ad9-885">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-885">Default Value</span></span> | <span data-ttu-id="c8ad9-886">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c8ad9-887">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-887">32 KB</span></span> | <span data-ttu-id="c8ad9-888">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c8ad9-889">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c8ad9-890">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c8ad9-891">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c8ad9-892">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-892">32 KB</span></span> | <span data-ttu-id="c8ad9-893">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c8ad9-894">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c8ad9-895">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-895">All Transports are enabled.</span></span> | <span data-ttu-id="c8ad9-896">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c8ad9-897">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-897">See below.</span></span> | <span data-ttu-id="c8ad9-898">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c8ad9-899">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-899">See below.</span></span> | <span data-ttu-id="c8ad9-900">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c8ad9-901">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c8ad9-902">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-902">Option</span></span> | <span data-ttu-id="c8ad9-903">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-903">Default Value</span></span> | <span data-ttu-id="c8ad9-904">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c8ad9-905">90 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-905">90 seconds</span></span> | <span data-ttu-id="c8ad9-906">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c8ad9-907">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c8ad9-908">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c8ad9-909">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-909">Option</span></span> | <span data-ttu-id="c8ad9-910">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-910">Default Value</span></span> | <span data-ttu-id="c8ad9-911">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c8ad9-912">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-912">5 seconds</span></span> | <span data-ttu-id="c8ad9-913">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c8ad9-914">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c8ad9-915">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c8ad9-916">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="c8ad9-916">Configure client options</span></span>

<span data-ttu-id="c8ad9-917">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c8ad9-918">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c8ad9-919">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="c8ad9-919">Configure logging</span></span>

<span data-ttu-id="c8ad9-920">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c8ad9-921">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c8ad9-922">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-923">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c8ad9-924">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c8ad9-925">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c8ad9-926">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c8ad9-927">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c8ad9-928">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c8ad9-929">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c8ad9-930">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c8ad9-931">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c8ad9-932">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c8ad9-933">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c8ad9-934">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c8ad9-935">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c8ad9-936">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c8ad9-937">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="c8ad9-937">Configure allowed transports</span></span>

<span data-ttu-id="c8ad9-938">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c8ad9-939">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c8ad9-940">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-940">All transports are enabled by default.</span></span>

<span data-ttu-id="c8ad9-941">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c8ad9-942">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c8ad9-943">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c8ad9-944">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="c8ad9-944">Configure bearer authentication</span></span>

<span data-ttu-id="c8ad9-945">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c8ad9-946">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c8ad9-947">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c8ad9-948">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c8ad9-949">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c8ad9-950">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c8ad9-951">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c8ad9-952">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c8ad9-953">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c8ad9-954">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="c8ad9-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c8ad9-955">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-956">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-957">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-957">Option</span></span> | <span data-ttu-id="c8ad9-958">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-958">Default value</span></span> | <span data-ttu-id="c8ad9-959">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c8ad9-960">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-961">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-961">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-962">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-963">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-964">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-965">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-965">15 seconds</span></span> | <span data-ttu-id="c8ad9-966">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-967">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-968">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-969">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-970">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-970">15 seconds</span></span> | <span data-ttu-id="c8ad9-971">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-972">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-973">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c8ad9-974">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-976">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-976">Option</span></span> | <span data-ttu-id="c8ad9-977">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-977">Default value</span></span> | <span data-ttu-id="c8ad9-978">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c8ad9-979">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-980">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-980">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-981">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c8ad9-982">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-983">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c8ad9-984">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-985">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-986">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-987">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-988">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-988">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-989">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-989">Option</span></span> | <span data-ttu-id="c8ad9-990">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-990">Default value</span></span> | <span data-ttu-id="c8ad9-991">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c8ad9-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c8ad9-993">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-994">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-994">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-995">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-996">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-997">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c8ad9-998">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-998">15 seconds</span></span> | <span data-ttu-id="c8ad9-999">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-1000">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-1001">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-1002">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c8ad9-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c8ad9-1004">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-1005">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c8ad9-1006">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c8ad9-1007">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c8ad9-1008">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1008">Configure additional options</span></span>

<span data-ttu-id="c8ad9-1009">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-1011">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1011">.NET Option</span></span> |  <span data-ttu-id="c8ad9-1012">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1012">Default value</span></span> | <span data-ttu-id="c8ad9-1013">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-1014">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c8ad9-1015">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-1016">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-1017">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c8ad9-1018">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1018">Empty</span></span> | <span data-ttu-id="c8ad9-1019">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c8ad9-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1020">Empty</span></span> | <span data-ttu-id="c8ad9-1021">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c8ad9-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1022">Empty</span></span> | <span data-ttu-id="c8ad9-1023">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c8ad9-1024">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1024">5 seconds</span></span> | <span data-ttu-id="c8ad9-1025">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1025">WebSockets only.</span></span> <span data-ttu-id="c8ad9-1026">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c8ad9-1027">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c8ad9-1028">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1028">Empty</span></span> | <span data-ttu-id="c8ad9-1029">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c8ad9-1030">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c8ad9-1031">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="c8ad9-1032">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c8ad9-1033">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c8ad9-1034">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c8ad9-1035">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c8ad9-1036">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c8ad9-1037">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c8ad9-1038">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c8ad9-1039">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-1041">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1041">JavaScript Option</span></span> | <span data-ttu-id="c8ad9-1042">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1042">Default Value</span></span> | <span data-ttu-id="c8ad9-1043">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c8ad9-1044">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c8ad9-1045">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c8ad9-1046">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-1047">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-1048">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-1049">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-1050">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1050">Java Option</span></span> | <span data-ttu-id="c8ad9-1051">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1051">Default Value</span></span> | <span data-ttu-id="c8ad9-1052">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-1053">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c8ad9-1054">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-1055">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-1056">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c8ad9-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c8ad9-1058">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1058">Empty</span></span> | <span data-ttu-id="c8ad9-1059">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c8ad9-1060">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c8ad9-1061">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c8ad9-1062">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c8ad9-1063">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c8ad9-1064">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-1065">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c8ad9-1066">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c8ad9-1067">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c8ad9-1068">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c8ad9-1069">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c8ad9-1070">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c8ad9-1071">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c8ad9-1072">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c8ad9-1073">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c8ad9-1074">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c8ad9-1075">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1075">MessagePack serialization options</span></span>

<span data-ttu-id="c8ad9-1076">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c8ad9-1077">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-1078">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c8ad9-1079">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1079">Configure server options</span></span>

<span data-ttu-id="c8ad9-1080">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c8ad9-1081">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1081">Option</span></span> | <span data-ttu-id="c8ad9-1082">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1082">Default Value</span></span> | <span data-ttu-id="c8ad9-1083">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-1084">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1084">15 seconds</span></span> | <span data-ttu-id="c8ad9-1085">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c8ad9-1086">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-1087">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c8ad9-1088">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1088">15 seconds</span></span> | <span data-ttu-id="c8ad9-1089">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c8ad9-1090">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c8ad9-1091">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c8ad9-1092">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1092">All installed protocols</span></span> | <span data-ttu-id="c8ad9-1093">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="c8ad9-1094">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c8ad9-1095">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c8ad9-1096">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c8ad9-1097">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c8ad9-1098">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c8ad9-1099">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c8ad9-1100">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c8ad9-1101">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c8ad9-1102">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c8ad9-1103">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1103">Option</span></span> | <span data-ttu-id="c8ad9-1104">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1104">Default Value</span></span> | <span data-ttu-id="c8ad9-1105">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c8ad9-1106">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1106">32 KB</span></span> | <span data-ttu-id="c8ad9-1107">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c8ad9-1108">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c8ad9-1109">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c8ad9-1110">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c8ad9-1111">32 КБ</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1111">32 KB</span></span> | <span data-ttu-id="c8ad9-1112">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c8ad9-1113">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c8ad9-1114">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1114">All Transports are enabled.</span></span> | <span data-ttu-id="c8ad9-1115">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c8ad9-1116">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1116">See below.</span></span> | <span data-ttu-id="c8ad9-1117">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c8ad9-1118">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1118">See below.</span></span> | <span data-ttu-id="c8ad9-1119">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c8ad9-1120">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c8ad9-1121">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1121">Option</span></span> | <span data-ttu-id="c8ad9-1122">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1122">Default Value</span></span> | <span data-ttu-id="c8ad9-1123">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c8ad9-1124">90 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1124">90 seconds</span></span> | <span data-ttu-id="c8ad9-1125">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c8ad9-1126">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c8ad9-1127">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c8ad9-1128">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1128">Option</span></span> | <span data-ttu-id="c8ad9-1129">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1129">Default Value</span></span> | <span data-ttu-id="c8ad9-1130">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c8ad9-1131">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1131">5 seconds</span></span> | <span data-ttu-id="c8ad9-1132">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c8ad9-1133">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c8ad9-1134">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c8ad9-1135">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1135">Configure client options</span></span>

<span data-ttu-id="c8ad9-1136">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c8ad9-1137">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c8ad9-1138">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1138">Configure logging</span></span>

<span data-ttu-id="c8ad9-1139">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c8ad9-1140">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c8ad9-1141">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c8ad9-1142">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c8ad9-1143">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c8ad9-1144">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c8ad9-1145">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c8ad9-1146">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c8ad9-1147">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c8ad9-1148">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c8ad9-1149">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c8ad9-1150">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c8ad9-1151">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c8ad9-1152">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c8ad9-1153">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c8ad9-1154">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c8ad9-1155">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c8ad9-1156">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1156">Configure allowed transports</span></span>

<span data-ttu-id="c8ad9-1157">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c8ad9-1158">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c8ad9-1159">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="c8ad9-1160">Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c8ad9-1161">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c8ad9-1162">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1162">Configure bearer authentication</span></span>

<span data-ttu-id="c8ad9-1163">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c8ad9-1164">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c8ad9-1165">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c8ad9-1166">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c8ad9-1167">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c8ad9-1168">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c8ad9-1169">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c8ad9-1170">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c8ad9-1171">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c8ad9-1172">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c8ad9-1173">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-1175">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1175">Option</span></span> | <span data-ttu-id="c8ad9-1176">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1176">Default value</span></span> | <span data-ttu-id="c8ad9-1177">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c8ad9-1178">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-1179">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1179">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-1180">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-1181">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-1182">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c8ad9-1183">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1183">15 seconds</span></span> | <span data-ttu-id="c8ad9-1184">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-1185">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c8ad9-1186">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-1187">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="c8ad9-1188">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-1190">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1190">Option</span></span> | <span data-ttu-id="c8ad9-1191">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1191">Default value</span></span> | <span data-ttu-id="c8ad9-1192">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c8ad9-1193">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-1194">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1194">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-1195">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c8ad9-1196">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-1197">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-1198">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-1199">Параметр</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1199">Option</span></span> | <span data-ttu-id="c8ad9-1200">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1200">Default value</span></span> | <span data-ttu-id="c8ad9-1201">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c8ad9-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c8ad9-1203">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c8ad9-1204">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1204">Timeout for server activity.</span></span> <span data-ttu-id="c8ad9-1205">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-1206">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c8ad9-1207">Рекомендуемое значение — это число, по крайней мере, двойное `KeepAliveInterval` значение сервера, которое позволяет получить время для проверки связи.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c8ad9-1208">15 секунд</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1208">15 seconds</span></span> | <span data-ttu-id="c8ad9-1209">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="c8ad9-1210">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c8ad9-1211">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c8ad9-1212">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c8ad9-1213">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1213">Configure additional options</span></span>

<span data-ttu-id="c8ad9-1214">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c8ad9-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c8ad9-1216">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1216">.NET Option</span></span> |  <span data-ttu-id="c8ad9-1217">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1217">Default value</span></span> | <span data-ttu-id="c8ad9-1218">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-1219">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c8ad9-1220">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-1221">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-1222">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c8ad9-1223">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1223">Empty</span></span> | <span data-ttu-id="c8ad9-1224">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c8ad9-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1225">Empty</span></span> | <span data-ttu-id="c8ad9-1226">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c8ad9-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1227">Empty</span></span> | <span data-ttu-id="c8ad9-1228">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c8ad9-1229">5 с</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1229">5 seconds</span></span> | <span data-ttu-id="c8ad9-1230">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1230">WebSockets only.</span></span> <span data-ttu-id="c8ad9-1231">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c8ad9-1232">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c8ad9-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1233">Empty</span></span> | <span data-ttu-id="c8ad9-1234">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c8ad9-1235">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c8ad9-1236">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="c8ad9-1237">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c8ad9-1238">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c8ad9-1239">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c8ad9-1240">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c8ad9-1241">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c8ad9-1242">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c8ad9-1243">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c8ad9-1244">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c8ad9-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c8ad9-1246">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1246">JavaScript Option</span></span> | <span data-ttu-id="c8ad9-1247">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1247">Default Value</span></span> | <span data-ttu-id="c8ad9-1248">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c8ad9-1249">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c8ad9-1250">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c8ad9-1251">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-1252">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-1253">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c8ad9-1254">Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="c8ad9-1255">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1255">Java Option</span></span> | <span data-ttu-id="c8ad9-1256">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1256">Default Value</span></span> | <span data-ttu-id="c8ad9-1257">Описание</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c8ad9-1258">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c8ad9-1259">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c8ad9-1260">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c8ad9-1261">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c8ad9-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c8ad9-1263">Empty</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1263">Empty</span></span> | <span data-ttu-id="c8ad9-1264">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c8ad9-1265">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c8ad9-1266">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c8ad9-1267">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c8ad9-1268">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c8ad9-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
